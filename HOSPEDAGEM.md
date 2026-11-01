# Hospedar o Emprega+ Itarema

O site é um aplicativo Node.js. O servidor atende às páginas, aos cadastros, pedidos, vagas, mensagens e uploads. Os dados ficam em `data/store.json` e `data/media/`. É necessário um serviço que execute `npm start` e um volume persistente.

## Alternativa: Cloudflare + Supabase

Essa combinação funciona para o Emprega+, mas exige migrar o backend atual antes do deploy. O ZIP desta versão não pode ser publicado como arquivos estáticos no Cloudflare Pages esperando que cadastro, pedidos e mensagens continuem funcionando.

- **Cloudflare Pages ou Workers Static Assets:** interface e domínio.
- **Supabase Auth:** cadastro, login e recuperação de conta.
- **Supabase Postgres:** perfis, anúncios, pedidos, propostas, vagas, candidaturas, mensagens, avaliações e avisos.
- **Supabase Storage:** fotos e vídeos; mídias dos anúncios podem ser públicas e fotos privadas de pedidos precisam de acesso controlado.
- **Supabase Edge Functions ou Cloudflare Worker:** regras de transição dos pedidos e processos seletivos, verificação de permissões e outras operações que não devem depender apenas do navegador.
- **RLS e políticas do Storage:** cada pessoa acessa somente seus pedidos, candidaturas e conversas; anúncios publicados continuam visíveis ao público.

Para um piloto no plano gratuito do Supabase, considere os limites atuais: 500 MB de banco, 1 GB de arquivos e até 50 MB por arquivo. O servidor atual aceita vídeos de até 60 MB, então o limite do site teria de ser reduzido ou os vídeos comprimidos. Projetos gratuitos com pouca atividade podem ser pausados. Essas condições devem ser conferidas antes de abrir o cadastro ao público.

A migração deve preservar os três tipos de conta e as etapas já desenhadas para serviços e vagas. Não basta trocar `DATA_DIR` por uma URL do Supabase: o código atual usa sessão por cookie e grava toda a base em um JSON local.

## Caminho sugerido: Railway

1. Extraia `emprega-plus.zip`. Coloque `package.json`, `server.js`, `public/`, `README.md` e os demais arquivos na raiz de um repositório GitHub. Não envie `data/` nem `.env`.
2. Na Railway, crie um projeto a partir desse repositório. O comando de início é `npm start`; se a plataforma não o detectar, configure-o em **Start Command**.
3. Anexe um volume ao serviço com ponto de montagem `/app/data`.
4. Nas variáveis do serviço, configure:

   | Nome | Valor |
   | --- | --- |
   | `HOST` | `0.0.0.0` |
   | `DATA_DIR` | `/app/data` |
   | `COOKIE_SECURE` | `1` |

   A Railway fornece `PORT` automaticamente. Não fixe uma porta diferente. Configure `ADMIN_EMAIL` apenas quando tiver uma conta administrativa cadastrada. `EMAIL_WEBHOOK_URL` e `EMAIL_WEBHOOK_TOKEN` são opcionais para avisos por e-mail.

5. Publique o serviço e, em **Settings → Networking**, use **Generate Domain** para obter um endereço inicial.
6. Para usar seu domínio, adicione **Custom Domain** na Railway e copie os registros DNS que ela mostrar para o provedor do domínio. Com Cloudflare, configure o CNAME e o TXT de verificação exatamente como exibidos.
7. Ative backups do volume. Teste cadastro, pedido, foto e mensagem; depois reinicie o serviço e confirme que continuam salvos.

Se preferir enviar a pasta local sem GitHub, a Railway também aceita um serviço vazio ligado pelo CLI com `railway link` e `railway up`. Faça isso a partir da pasta extraída que contém `package.json`.

## Limite da versão atual

Esta versão usa um arquivo JSON e foi pensada para uma única instância e testes/piloto. Para abrir cadastros ao público em escala, faltam verificação e recuperação de conta, política de privacidade, rotina de moderação e monitoramento. Planeje migrar o arquivo JSON para um banco de dados e os uploads para armazenamento de objetos antes de crescer.

Cloudflare pode gerenciar o domínio e o DNS, mas o ZIP atual não funciona como um site estático no Cloudflare Pages: ele depende do servidor Node e de arquivos persistentes.

## Documentação oficial

- [Cloudflare: sistema de arquivos temporário dos Workers](https://developers.cloudflare.com/workers/runtime-apis/nodejs/fs/)
- [Supabase: arquitetura de Auth, Postgres, Storage e Functions](https://supabase.com/docs/guides/getting-started/architecture)
- [Supabase: segurança por linha (RLS)](https://supabase.com/docs/guides/database/postgres/row-level-security)
- [Supabase: limites de armazenamento no plano gratuito](https://supabase.com/docs/guides/platform/billing-on-supabase)
- [Supabase: limite por arquivo no plano gratuito](https://supabase.com/docs/guides/storage/uploads/file-limits)
- [Railway: serviços e deploy a partir do GitHub ou pasta local](https://docs.railway.com/services)
- [Railway: volumes persistentes e ponto de montagem](https://docs.railway.com/volumes)
- [Railway: host e porta para aplicações web](https://docs.railway.com/networking/troubleshooting/application-failed-to-respond)
- [Railway: domínio e HTTPS](https://docs.railway.com/networking/domains/working-with-domains)
- [Railway: backups de volumes](https://docs.railway.com/volumes/backups)
