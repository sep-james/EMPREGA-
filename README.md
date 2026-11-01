# Emprega+ · Itarema

Aplicativo local para encontrar serviços e produtos de Itarema e acompanhar oportunidades de trabalho. A identidade usa o símbolo de Itarema enviado para o projeto. O Emprega+ é independente da prefeitura.

## Rodar no seu computador

1. Instale Node.js 20 ou superior.
2. Abra o PowerShell nesta pasta.
3. Execute:

```powershell
npm start
```

4. Acesse [http://localhost:3000](http://localhost:3000).

O projeto não precisa de `npm install`. Para escolher outra porta:

```powershell
$env:PORT='3104'
npm start
```

O servidor aceita conexões apenas do próprio computador por padrão (`HOST=127.0.0.1`). Pare com Ctrl+C.

## O que funciona

- **Serviços:** busca por categoria, localidade, tipo de atendimento e disponibilidade. O anunciante pode selecionar várias formas de atendimento, publicar fotos e vídeos e informar a duração apenas quando fizer sentido.
- **Pedidos:** o cliente descreve o que precisa e escolhe a forma de atendimento. O local só é pedido quando o serviço será feito em sua casa; dia, horário e fotos são opcionais. O anunciante responde com escopo e valor, podendo propor um agendamento ou pedir mais informações. O cliente aceita ou pede ajustes. Quando há agendamento, o site reserva o horário e permite remarcação. Ambos acompanham andamento, cancelamento, problema, conclusão e avaliação.
- **Produtos:** aba própria para todos os perfis, anúncios com fotos/vídeos e pedidos acompanhados por comprador e vendedor. O comprador informa quantidade e escolhe retirada, entrega ou combinação. O vendedor confirma disponibilidade e valor total; o comprador aceita ou pede ajuste. O histórico acompanha entrega/retirada e recebimento. Não há pagamento integrado.
- **Vagas:** empresa publica, edita, pausa e encerra vagas; candidato envia perfil e acompanha a candidatura; empresa analisa, convida para entrevista, remarca, envia proposta e registra contratação após aceite do candidato.
- **Talentos:** cidadãos e trabalhadores podem tornar seu perfil visível; empresas convidam para uma vaga sem criar candidatura em nome deles.
- **Mensagens e avisos:** conversas vinculadas a pedido, candidatura, produto ou contato geral. Avisos persistem na conta.
- **Contas:** cadastro com três tipos. Cidadão busca serviços, produtos e vagas; trabalhador anuncia e também pode se candidatar; empresa anuncia e contrata. A navegação e a área de gestão mostram as ações de cada tipo. Perfil, foto, capa e publicações são editáveis; anunciantes também gerenciam horários e disponibilidade.
- **Moderação:** denúncias são registradas. Configure `ADMIN_EMAIL` para habilitar a área restrita de análise de denúncias.

As telas usam endereços próprios: `/servicos`, `/pedidos`, `/vagas`, `/candidaturas`, `/talentos`, `/produtos`, `/pedidos-produtos` e `/minha-area`. Links diretos, voltar e recarregar funcionam.

## Gerenciamento

**Minha área** mostra primeiro o que precisa de resposta. Pedidos e candidaturas aparecem em quatro grupos: **Precisa de você**, **Aguardando resposta**, **Em andamento** e **Encerrados**. Cada item mostra o próximo passo e abre a conversa, o histórico e as ações daquele processo.

Em **Meus serviços** e **Meus produtos**, cada anúncio leva aos próprios pedidos e à edição. Em **Minhas vagas**, cada vaga leva aos seus candidatos e à edição. No detalhe, decisões secundárias como remarcação e recusa ficam recolhidas. O planejamento completo dos papéis e etapas está em [FLUXOS-DE-GESTAO.md](FLUXOS-DE-GESTAO.md).

## Dados e atualização

Contas, anúncios, pedidos, produtos, vagas, candidaturas, avaliações e conversas ficam em `data/store.json`. Arquivos ficam em `data/media/`. **Faça cópia da pasta `data/` antes de atualizar o código.** O novo servidor lê os registros da versão anterior e acrescenta os novos campos quando necessário. O ZIP entregue contém o código e as marcas, sem contas ou mensagens pessoais.

Para guardar dados em outro diretório, defina `DATA_DIR` antes de iniciar. Nunca coloque a pasta de dados em um diretório público do servidor.

## Avisos por e-mail e administração

Os avisos dentro do site funcionam sem configuração. Para enviar e-mails importantes, configure uma URL HTTPS em `EMAIL_WEBHOOK_URL`. O servidor envia POST JSON com `to`, `subject`, `text` e `path`. Se o webhook exigir autenticação, configure `EMAIL_WEBHOOK_TOKEN`; a requisição usará `Authorization: Bearer ...`. Falhas ficam pendentes para nova tentativa. Sem webhook, nenhum e-mail é enviado.

Defina `ADMIN_EMAIL` com o e-mail de uma conta cadastrada para liberar `/admin/denuncias`. O administrador pode registrar a decisão e pausar anúncios ou vagas. Configure esses valores como variáveis do ambiente; não coloque senhas ou tokens no código.

## Antes de hospedar para o público

O passo a passo para colocar esta versão em um servidor Node com armazenamento persistente está em [HOSPEDAGEM.md](HOSPEDAGEM.md).

Esta versão funciona para desenvolvimento e testes locais. Para operação pública contínua, configure HTTPS, armazenamento persistente com backups, entrega de e-mail, recuperação/verificação de conta, política de privacidade, moderação ativa e monitoramento. O arquivo JSON local deve ser migrado para um banco de dados antes de crescer em usuários ou processos simultâneos. Pagamentos, videoconferência e documentos formais de contratação não são processados pelo site.

Para hospedar um servidor Node, defina `HOST=0.0.0.0`, a `PORT` exigida pela hospedagem, `COOKIE_SECURE=1` sob HTTPS e `DATA_DIR` em um volume persistente. Nunca hospede com uma pasta de dados descartável.
