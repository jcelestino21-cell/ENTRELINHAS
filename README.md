# ENTRELINHAS

Aplicação web em Python 3, sem dependências externas: interface web + API HTTP + SQLite. O banco é criado automaticamente e recebe 50 perguntas em 10 categorias na primeira execução.

## Rodar localmente

```bash
ADMIN_PASSWORD='defina-uma-senha-forte-e-unica' SESSION_SECRET='gere-um-segredo-aleatorio-longo' python app.py
```

Abra http://localhost:8000. Para uso local sem configurar a senha, a área privada fica deliberadamente desabilitada; as respostas de teste não devem ser consideradas armazenadas em ambiente de produção.

## Publicação real (Render)

1. Envie `app.py` e `render.yaml` para um repositório GitHub.
2. No Render, crie um **Blueprint** a partir do repositório e confirme o serviço web.
3. Informe `ADMIN_PASSWORD` como variável secreta, usando senha forte, única e não compartilhada.
4. O Blueprint configura `SESSION_SECRET` gerado e um disco persistente montado em `/var/data`; o banco SQLite fica nesse disco. Confirme no serviço que o disco persistente está habilitado no seu plano.
5. Aguarde o deploy e teste o URL HTTPS fornecido pelo Render. Esse é o link público compartilhável. Use `/` para participantes e o botão **Área privada** para administrar.

Não publique sem `ADMIN_PASSWORD` e sem confirmar o disco persistente. Em planos sem disco persistente, dados podem ser perdidos em reinicializações/deploys. Faça backups periódicos do arquivo SQLite pelo mecanismo de backup do provedor.

## Funcionalidades presentes

- 50 perguntas distribuídas em 10 categorias; cada sessão sorteia 18 perguntas, com variedade de categorias.
- Uma pergunta por tela, respostas abertas/alternativas, navegação anterior, progresso, resposta opcional, autosave de sessão no navegador e envio final.
- Identificador único, nome opcional, timestamp UTC, versão, perguntas/respostas e estado de conclusão armazenados em SQLite.
- Painel autenticado por cookie HttpOnly assinado, consulta de participações e respostas, busca, resumo de categorias, exportação CSV, exclusão e edição/ativação de perguntas.
- Campo privado de notas de leitura qualitativa, sem pontuação ou diagnóstico automatizado.

## Limites/configuração importante

- O preview local desta sessão demonstra a aplicação, mas não cria por si só uma publicação pública persistente. A publicação exige repositório/conta de hospedagem, configuração do segredo e disco persistente.
- A autenticação usa senha secreta do ambiente e assinatura HMAC; `SESSION_SECRET` deve ser estável entre reinícios para manter sessões. Não há usuários múltiplos/recuperação de senha.
- SQLite serve bem para um site de baixo volume em uma instância. Não escale para múltiplas instâncias/alta concorrência sem migrar para PostgreSQL e rever o deployment.
- As respostas são privadas por autenticação do painel, mas o administrador deve proteger sua senha e o provedor/servidor. A publicação HTTPS do Render protege o tráfego em trânsito.
- O armazenamento de rascunho é local ao navegador até o envio; o armazenamento persistente no servidor começa após o botão de conclusão.
