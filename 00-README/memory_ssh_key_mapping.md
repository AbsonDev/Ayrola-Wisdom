Ayrola-Wisdom — mapeamento de chaves SSH para contas GitHub:
- id_ed25519 (absondutragalvao@bitbucket) -> Bitbucket, NAO usar para GitHub
- id_ed25519_absinventione (absondutragalvao@github-absinventione) -> AbsonInventione
- id_ed25519_github (abson.dutra@inventione.com.br) -> AbsonDev (USAR PARA AYROLA)
- agent_staging -> agent-staging
- filazero-automacoes -> filazero-automation-mcp
- filazero-windows-dev -> filazero-windows-dev
- id_ed25519_absinventione.pub ja cadastrada no GitHub como AbsonInventione
- id_ed25519_github.pub ja cadastrada no GitHub como AbsonDev
- Comando para push com chave correta: GIT_SSH_COMMAND='ssh -i ~/.ssh/id_ed25519_github -o BatchMode=yes' git push origin main