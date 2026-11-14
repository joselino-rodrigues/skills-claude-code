# Skill: Site Security

## Visão geral

Esta skill tem como objetivo reforçar a segurança de sites e sistemas digitais, com foco em brechas que deixam a infraestrutura vulnerável mesmo quando a camada externa parece protegida.

A principal ideia é simples: não basta parecer seguro; é preciso estar protegido de verdade.

## Problema central

Muitas configurações deixam portas alternativas abertas para invasores. Em vez de atacar pela porta da frente, o hacker pode encontrar um caminho lateral, como:

- endereço real do servidor exposto
- subdomínios antigos e esquecidos ativos
- acesso direto ao servidor pela internet
- rota entre proxy e origem sem proteção correta
- TLS em modo inadequado, como `Flexible`, em vez de `Full Strict`

## O que esta skill corrige

### 1. Exposição do endereço real do servidor

Revisar registros públicos, infraestrutura antiga, e-mails de sistema e possíveis caminhos que revelem o host real da aplicação.

### 2. Bloqueio de acessos diretos

Garantir que o servidor não aceite tráfego livre da internet e que ele converse apenas com a rede de proteção correta, como o Cloudflare.

### 3. Proteção completa do trânsito

A comunicação precisa estar segura em toda a rota:

- do cliente até o proxy
- do proxy até a origem
- do servidor até o certificado e a camada de TLS

A configuração recomendada é `Full Strict`, com certificado válido instalado diretamente no servidor.

## Checklist de segurança

Antes de considerar um site protegido, confirme que:

- [ ] o endereço real do servidor não está exposto indevidamente
- [ ] subdomínios antigos foram revisados
- [ ] o servidor não está acessível diretamente pela internet
- [ ] o tráfego passa pela rede de proteção correta
- [ ] o HTTPS está configurado em `Full Strict`
- [ ] o certificado TLS está válido e instalado corretamente
- [ ] não há rota aberta entre Cloudflare e origem

## Diagnóstico

A falsa sensação de segurança surge quando o site parece estar protegido por um proxy ou WAF, mas a origem continua aberta, exposta ou configurada de forma inadequada. Nesse cenário, o atacante não precisa quebrar a camada externa; ele simplesmente usa a rota alternativa.

## Entregáveis

- revisão de infraestrutura e DNS
- identificação de pontos de exposição
- bloqueio do acesso direto à origem
- validação de configuração TLS/HTTPS
- orientação para hardening de segurança da camada do servidor

## Resultado esperado

O site passa a operar com proteção real, em vez de apenas aparência de segurança. Isso reduz o risco de exploração, fortalece a confiabilidade da infraestrutura e melhora a postura geral de segurança do projeto.

## Tecnologias e conceitos envolvidos

- Cloudflare
- DNS
- HTTPS / TLS
- WAF
- firewall e regras de acesso
- proteção de origem
- subdomínios e infraestrutura legada

## Status

Ativa / em desenvolvimento

