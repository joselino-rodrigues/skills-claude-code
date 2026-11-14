# Skill: Web Performance

## Visão geral

Esta skill ajuda a diagnosticar e melhorar a velocidade percebida e real de sites e aplicações. Abrange frontend, rede, APIs, backend e banco de dados.

O princípio central é medir antes e depois das mudanças. Otimizações devem responder a um gargalo identificado, preservar a funcionalidade e ser verificadas em condições próximas às de uso real.

## Escopo

Use esta skill para investigar carregamento lento, interações atrasadas, APIs lentas, alto consumo de recursos ou problemas de escalabilidade. Considere o tipo de aplicação, os dispositivos e as redes dos usuários, além dos custos e da complexidade operacional de cada solução.

## Processo recomendado

1. Defina as páginas, jornadas e operações mais importantes.
2. Meça uma linha de base em produção ou em ambiente representativo.
3. Identifique o gargalo com ferramentas de navegador, métricas de campo, tracing e análise de consultas.
4. Priorize mudanças pelo impacto para o usuário, frequência do problema e custo/risco da solução.
5. Altere uma causa de cada vez quando possível e compare os resultados com a linha de base.
6. Monitore regressões após a publicação.

## Otimizações por camada

### 1. Experiência no frontend

- [ ] Mostre estados de carregamento adequados, como skeletons, quando reduzirem a percepção de espera; preserve estados vazios e de erro.
- [ ] Divida o JavaScript por página ou funcionalidade e carregue sob demanda o que não é necessário na primeira tela.
- [ ] Carregue imagens abaixo da área visível sob demanda e defina dimensões para reduzir deslocamentos de layout.
- [ ] Sirva imagens em formatos modernos, como WebP ou AVIF, com alternativas compatíveis e dimensões adequadas ao dispositivo.
- [ ] Evite renderizações, cálculos e atualizações de estado desnecessários; virtualize listas realmente longas.
- [ ] Considere SSR ou geração estática para páginas em que isso melhore carregamento, indexação ou experiência, avaliando o custo de renderização e a necessidade de conteúdo dinâmico.
- [ ] Use atualização otimista da interface somente quando for possível reverter ou comunicar claramente uma falha da operação.
- [ ] Para respostas de IA ou conteúdo gerado, considere streaming quando reduzir a espera percebida e o protocolo e a interface suportarem isso.
- [ ] Carregue scripts de terceiros apenas quando necessários e, quando possível, depois do conteúdo principal.
- [ ] Hospede fontes localmente quando isso for vantajoso e limite famílias e pesos às variantes utilizadas.

### 2. Rede, APIs e entrega de conteúdo

- [ ] Elimine chamadas duplicadas e dependências em série desnecessárias; execute em paralelo apenas requisições independentes.
- [ ] Aplique debounce a buscas e preenchimento automático para evitar chamadas a cada tecla; mantenha a interface responsiva.
- [ ] Pagine ou carregue incrementalmente conjuntos grandes de resultados.
- [ ] Defina timeouts adequados para chamadas externas e trate falhas de forma explícita; use retries limitados apenas quando forem seguros e apropriados.
- [ ] Sirva arquivos estáticos por CDN quando a arquitetura e o perfil de tráfego justificarem.
- [ ] Configure cache HTTP para arquivos estáticos, com versionamento ou nomes com hash para permitir cache longo sem servir conteúdo desatualizado.
- [ ] Ative compressão como Brotli ou Gzip conforme o suporte da infraestrutura e o tipo de conteúdo; evite comprimir formatos que já são comprimidos.

### 3. Backend e banco de dados

- [ ] Corrija consultas N+1 e reduza viagens desnecessárias ao banco.
- [ ] Selecione apenas as colunas e linhas necessárias.
- [ ] Crie ou ajuste índices com base em consultas frequentes e planos de execução; considere o custo dos índices em escrita e armazenamento.
- [ ] Reutilize conexões por meio do pool da biblioteca/driver ou de um pooler externo, como PgBouncer para PostgreSQL, quando apropriado.
- [ ] Dimensione o pool considerando o limite de conexões do banco, as conexões reservadas para administração e o número máximo de instâncias da aplicação. Some os pools de todas as instâncias antes de definir o limite por processo.
- [ ] Configure timeout para obter conexão do pool, duração máxima de conexão e comportamento quando o pool estiver saturado; retorne erro controlado em vez de manter requisições esperando indefinidamente.
- [ ] Mova tarefas demoradas ou não interativas para filas em segundo plano, com tratamento de falhas, repetição segura e observabilidade.
- [ ] Configure timeouts e limites para chamadas a serviços externos e para operações de banco de dados.
- [ ] Considere cache para dados acessados com frequência somente depois de definir expiração, invalidação e tolerância a dados desatualizados.
- [ ] Use réplicas de leitura ou distribuição de tráfego entre instâncias quando medições e requisitos de escala justificarem a complexidade.
- [ ] Avalie a proximidade entre aplicação e banco de dados para reduzir latência, respeitando requisitos de disponibilidade e residência de dados.

Um pool mantém um conjunto limitado de conexões reutilizáveis. Cada operação obtém uma conexão disponível, executa o trabalho e a devolve ao pool; se todas estiverem ocupadas, novas operações aguardam até uma conexão ficar livre ou o timeout expirar. Isso evita o custo repetido de estabelecer conexões e ajuda a limitar a concorrência no banco, mas não acelera consultas lentas por si só. Pools excessivamente grandes podem consumir os recursos do banco e pools pequenos demais podem criar filas e aumentar a latência.

Os limites de conexões do PostgreSQL e de outros bancos variam por configuração, versão e provedor; não presuma um valor padrão fixo. PgBouncer e outros poolers externos são alternativas para centralizar ou reduzir conexões, especialmente em arquiteturas com muitas instâncias ou conexões curtas. Escolha o modo e a configuração compatíveis com o uso da aplicação, monitore conexões ativas, tempo de espera, saturação e latência, e valide o comportamento sob carga representativa.

### 4. Build, dependências e infraestrutura

- [ ] Remova dependências não utilizadas e revise o tamanho do bundle de produção.
- [ ] Configure compressão, cache e entrega de arquivos estáticos na infraestrutura efetivamente usada.
- [ ] Use balanceamento entre instâncias quando necessário para capacidade ou disponibilidade, com health checks e gerenciamento adequado de sessões.
- [ ] Evite adicionar infraestrutura ou camadas de cache sem evidência de que resolvem um gargalo real.

## Métricas e validação

- [ ] Registre métricas de campo e acompanhe Core Web Vitals, como LCP, INP e CLS, quando aplicável.
- [ ] Use tracing distribuído, como OpenTelemetry, para localizar requisições lentas entre serviços; evite coletar dados sensíveis nos atributos e logs.
- [ ] Acompanhe latência de APIs, erros, consultas lentas, tamanho do JavaScript e tempos de carregamento.
- [ ] Teste em celulares e em condições de rede e CPU limitadas, além de dispositivos e redes rápidos.
- [ ] Compare resultados antes e depois com as mesmas páginas, jornadas e condições de teste.
- [ ] Defina metas e orçamentos de performance realistas para os fluxos mais importantes e monitore regressões.

## Pontos de atenção

- Cache, índices, réplicas e execução paralela podem introduzir dados desatualizados, custo ou novas falhas; implemente apenas com critérios claros.
- Connection pooling reduz o custo e limita a concorrência de conexões, mas exige dimensionamento e timeouts. O resultado de um benchmark depende do banco, da rede, das consultas e do ambiente; não prometa ganhos fixos sem medi-los.
- Skeletons não tornam a operação mais rápida por si só; melhoram apenas a percepção de carregamento quando usados corretamente.
- SSR, geração estática, CDN e filas não são soluções universais. Escolha com base nos requisitos e nas medições.
- Não exponha dados pessoais ou segredos em telemetria, logs, traces ou ferramentas de monitoramento.

## Entregáveis

- linha de base e resumo dos gargalos observados
- lista priorizada de melhorias com impacto e trade-offs
- mudanças de performance acompanhadas de validação
- plano de monitoramento para detectar regressões

## Resultado esperado

As páginas e operações prioritárias carregam e respondem melhor para os usuários, com melhorias verificadas por métricas e sem introduzir complexidade desnecessária ou degradar a funcionalidade.

## Tecnologias e conceitos envolvidos

- Core Web Vitals e ferramentas de medição de desempenho
- CDN, cache HTTP e compressão
- formatos de imagem WebP e AVIF
- JavaScript splitting, lazy loading e virtualização
- APIs, debounce, paginação e streaming
- filas, tracing e OpenTelemetry
- otimização de consultas, índices e pooling de conexões

## Status

Ativa / em desenvolvimento
