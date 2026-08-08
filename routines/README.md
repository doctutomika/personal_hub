# routines/ — specs das rotinas de nuvem (fonte de verdade)

Este diretório guarda as **especificações canônicas** das rotinas que geram os conteúdos diários do Personal Hub. A ideia: **o repositório é a fonte de verdade**. O trigger agendado na nuvem é só um **lançador fino** que lê a spec daqui e a segue. Para mudar uma rotina, **edite o arquivo e faça commit** — não precisa mais copiar/colar no prompt do trigger.

## Arquivos
- [`mir-health.md`](./mir-health.md) — MIR Health (report de saúde), cron ~5h30.
- [`did-diario.md`](./did-diario.md) — DID (mercados), cron ~5h15.

## Como o cron consome a spec
O trigger tem um prompt curto que manda a sessão buscar a spec por HTTP (o repo é público; o cron só precisa de `WebFetch`, sem token):

```
WebFetch https://raw.githubusercontent.com/doctutomika/personal_hub/main/routines/mir-health.md
```

Prompts-lançadores sugeridos para os triggers (colar no lugar da spec embutida, depois que estes arquivos estiverem na main):

**mir-nuvem**
```
Gere o MIR Health de HOJE e publique via API. A especificação canônica e completa
está no repositório. Leia-a inteira e siga-a à risca:
WebFetch https://raw.githubusercontent.com/doctutomika/personal_hub/main/routines/mir-health.md
Segredos nas env vars $HUB_TOKEN e $OPENAI_API_KEY (use só via shell, nunca em texto).
Se o WebFetch falhar, tente 1x mais antes de abortar e reporte em 1 linha.
```

**did-diario-nuvem**
```
Gere o DID de HOJE e publique via API. A especificação canônica e completa está
no repositório. Leia-a inteira e siga-a à risca:
WebFetch https://raw.githubusercontent.com/doctutomika/personal_hub/main/routines/did-diario.md
Segredos nas env vars $HUB_TOKEN e $OPENAI_API_KEY (use só via shell, nunca em texto).
Se o WebFetch falhar, tente 1x mais antes de abortar e reporte em 1 linha.
```

## Notas
- `raw.githubusercontent.com` tem cache de CDN de ~5 min — irrelevante para cron diário.
- **Privacidade:** enquanto o repo estiver público, nada sensível deve entrar nas specs. Os nomes internos (clientes/empreendimentos/marcas pessoais/consultório) foram abstraídos nas regras de "não citar" — a instrução ao gerador continua a mesma, sem nomear ninguém. Ao privatizar o repo, dá para reintroduzir nomes se fizer sentido.
- **Ordem de corte:** os lançadores só funcionam depois que estes arquivos estiverem na `main` (o cron lê a main). Faça o merge antes de trocar os prompts dos triggers.
