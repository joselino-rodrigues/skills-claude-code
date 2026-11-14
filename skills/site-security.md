# Skill: Site Security

## Visão geral

Esta skill ajuda a revisar e fortalecer a segurança de sites e aplicações antes da publicação e durante a operação. O foco é encontrar configurações que podem passar despercebidas, desde segredos expostos no frontend até autenticação, limitação de requisições e proteção da infraestrutura.

Segurança não deve depender apenas da aparência do site ou da existência de um proxy/WAF: cada camada precisa ser configurada e validada.

## Escopo

Use esta skill apenas em aplicações e infraestruturas próprias ou para as quais exista autorização explícita. Faça verificações que possam afetar disponibilidade ou dados em ambiente de teste/staging sempre que possível.

## Validações antes de publicar

### 1. Variáveis de ambiente e segredos

Variáveis usadas no frontend podem ser incorporadas ao JavaScript entregue ao navegador. Considere qualquer valor presente no bundle, em source maps ou em respostas públicas como acessível a qualquer visitante.

- [ ] Revise as variáveis e configurações incluídas no build do frontend.
- [ ] Pesquise no código compilado e nos source maps por nomes e valores sensíveis conhecidos, como chaves privadas, tokens e credenciais.
- [ ] Confirme que apenas variáveis intencionalmente públicas são expostas. Prefixos como `VITE_`, `NEXT_PUBLIC_` e `REACT_APP_` normalmente indicam valores destinados ao cliente; não coloque segredos neles.
- [ ] Mantenha credenciais, chaves privadas e tokens de serviço no servidor, em um gerenciador de segredos ou em variáveis protegidas do ambiente de execução.
- [ ] Se um segredo foi publicado, remova-o do cliente e revogue/rotacione a credencial. Apagar o valor do código atual não invalida cópias já distribuídas.

As ferramentas de desenvolvedor do navegador podem ajudar a inspecionar os recursos carregados, mas encontrar ou não um nome específico não substitui a revisão da configuração de build e do código-fonte.

### 2. Autenticação, sessão e autorização

Apagar cookies é um teste útil de persistência de sessão, mas, sozinho, não comprova que a autenticação está segura. Tokens podem estar armazenados em outros locais e a proteção de páginas feita apenas no frontend pode ser contornada.

- [ ] Em uma conta de teste, encerre a sessão e limpe os cookies e os dados de armazenamento do site; recarregue a aplicação.
- [ ] Confirme que páginas e dados privados não ficam acessíveis sem autenticação válida.
- [ ] Teste os endpoints privados diretamente em ambiente autorizado: a API deve validar a sessão e as permissões em cada requisição, sem confiar apenas na interface ou em rotas protegidas pelo cliente.
- [ ] Verifique que logout, expiração e revogação invalidam a sessão no servidor quando aplicável.
- [ ] Configure cookies de sessão com atributos adequados, como `HttpOnly`, `Secure` e `SameSite`, conforme o fluxo da aplicação.
- [ ] Confira que a aplicação não mantém tokens ou dados sensíveis em armazenamento acessível a scripts sem uma necessidade justificada.

### 3. Rate limiting

Implemente limitação de requisições no servidor para reduzir tentativas automatizadas e abuso de endpoints. Não dependa de controles apenas no navegador.

- [ ] Aplique limites a login, recuperação de senha, criação de conta e outros endpoints sensíveis.
- [ ] Defina limites e janelas conforme o risco, o uso legítimo e a arquitetura; considere contas e sinais adicionais além do endereço IP.
- [ ] Responda de forma consistente quando o limite for excedido e registre eventos relevantes sem armazenar senhas, tokens ou outros segredos nos logs.
- [ ] Evite bloqueios permanentes ou regras que permitam facilmente negar serviço a usuários legítimos.
- [ ] Teste os limites em ambiente controlado e monitore falsos positivos após a publicação.

## Proteção da infraestrutura e do tráfego

### Endereço da origem e acesso direto

- [ ] Revise registros DNS atuais e, quando apropriado, históricos; um registro antigo pode revelar um endereço anteriormente usado, mas não prova que ele ainda hospeda a aplicação.
- [ ] Inventarie subdomínios, serviços auxiliares e infraestrutura legada. Confirme se cada host ainda é necessário e se está protegido de acordo com sua função.
- [ ] Revise cabeçalhos de e-mails transacionais enviados pelo sistema para identificar divulgação desnecessária de detalhes de infraestrutura. O IP do servidor de envio não é necessariamente o IP da origem web; prefira um provedor de e-mail dedicado quando adequado.
- [ ] Avalie se endereços da origem aparecem em registros públicos, aplicações ou serviços auxiliares. A exposição do endereço, por si só, não significa bypass: o impacto depende dos controles de acesso e dos serviços disponíveis na origem.
- [ ] Se houver proxy/CDN, restrinja o acesso à origem conforme a arquitetura e os endereços oficiais do provedor, mantendo exceções necessárias para administração e verificações de saúde. Planeje a atualização dos intervalos permitidos e valide as regras para não interromper tráfego legítimo.
- [ ] Teste o acesso direto à origem somente em ativos próprios ou autorizados e confirme que os controles esperados bloqueiam ou limitam esse tráfego.
- [ ] Se um endereço de origem ativo tiver sido exposto, avalie a rotação do endereço e atualize DNS, regras de firewall e integrações dependentes; não trate a troca do IP como substituta dos controles de acesso.
- [ ] Proteja serviços administrativos e portas de gerenciamento com controles de acesso apropriados.

### TLS entre proxy e origem

O TLS deve proteger tanto o trecho entre o navegador e o proxy quanto o trecho entre o proxy e o servidor de origem.

- [ ] Use HTTPS com validação do certificado também entre o proxy e a origem.
- [ ] No Cloudflare, prefira `Full (strict)` quando a origem estiver configurada com certificado válido ou com um certificado de origem aceito pelo Cloudflare.
- [ ] Não use `Flexible` como substituto para configurar TLS na origem: nesse modo, o trecho do Cloudflare até o servidor não usa HTTPS.
- [ ] Confirme renovação, validade, hostname e cadeia do certificado, além do comportamento de redirecionamentos HTTP para HTTPS.
- [ ] Trate TLS como uma camada da defesa: `Full (strict)` protege e valida o trecho Cloudflare–origem, mas não substitui autenticação, autorização, firewall ou proteção da aplicação.

## Processo recomendado

1. Identifique a arquitetura e os limites de confiança: navegador, aplicação, API, proxy e origem.
2. Faça as verificações em ambiente próprio e autorizado, preferencialmente em staging.
3. Registre evidências sem incluir segredos ou dados pessoais.
4. Corrija primeiro exposições de credenciais e falhas de autorização; depois ajuste limites e infraestrutura.
5. Repita os testes após as mudanças e monitore a aplicação em produção.

## Entregáveis

- checklist de publicação preenchido
- registro dos riscos encontrados e sua prioridade
- recomendações ou alterações para variáveis, autenticação, rate limiting, TLS e acesso à origem
- evidências de validação sem segredos ou dados pessoais

## Resultado esperado

A aplicação evita expor segredos no cliente, valida autenticação e autorização no servidor, limita tentativas abusivas e protege adequadamente o tráfego e a infraestrutura. Nenhuma lista garante segurança absoluta; as verificações devem acompanhar mudanças na aplicação e nos serviços utilizados.

## Tecnologias e conceitos envolvidos

- Cloudflare e CDN/WAF
- DNS e infraestrutura de origem
- variáveis de ambiente e bundles frontend
- autenticação, autorização e gerenciamento de sessão
- rate limiting
- HTTPS / TLS
- firewall e logs de segurança

## Status

Ativa / em desenvolvimento
