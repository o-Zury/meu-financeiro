# Meu Financeiro — versão com Firebase

Esta versão mantém o layout e os recursos do Meu Financeiro, mas substitui o armazenamento local como fonte principal por Cloud Firestore e login Firebase Authentication. O GitHub Pages publica os arquivos estáticos; o Firestore guarda os dados privados na nuvem.

## Arquivos
- `index.html`: site.
- `firebase-config.js`: configuração pública do app Firebase (você preencherá).
- `firestore.rules`: regras de segurança para restringir dados ao UID autenticado.

## 1. Criar o projeto Firebase sem ativar cobrança
1. Acesse https://console.firebase.google.com/ e entre com sua conta Google.
2. Crie um projeto chamado `Meu Financeiro` (Google Analytics é opcional; pode deixar desativado).
3. Confirme que o plano é **Spark**. Não faça upgrade para Blaze e não vincule faturamento.
4. Em `Configurações do projeto` > `Seus apps`, registre um app da Web (`</>`), por exemplo `Meu Financeiro Web`.
5. Copie o objeto `firebaseConfig` exibido pelo Firebase e preencha os campos correspondentes de `firebase-config.js`. Não envie senha ou chave privada para ninguém.

## 2. Ativar login por e-mail e senha
1. No Firebase Console, abra `Authentication` > `Começar` (se solicitado).
2. Em `Sign-in method`, ative `E-mail/senha` e salve.
3. Em `Users`, use `Add user` para criar sua conta pessoal com seu e-mail e uma senha forte.
4. Na lista de usuários, copie o **UID** da sua conta.
5. Abra `firestore.rules`, substitua `COLE_AQUI_SEU_UID` pelo UID copiado e só então publique as regras no Firestore. Isso restringe o banco à sua conta específica, mesmo que outra pessoa tente criar um usuário separado.
6. Não há botão de cadastro público no site.

## 3. Criar o banco e aplicar as regras
1. Abra `Firestore Database` > `Create database`.
2. Escolha **Production mode**. Selecione uma localização próxima, como `southamerica-east1` (São Paulo), caso disponível. A localização do banco não pode ser alterada depois.
3. Na área `Rules`, substitua o conteúdo pelas regras de `firestore.rules` e clique em `Publish`.
4. O site grava os dados nestes caminhos: `users/{UID}/transactions`, `users/{UID}/budgets` e `users/{UID}/settings/categories`. As regras permitem acesso somente ao UID único que você inseriu no arquivo `firestore.rules`.

## 4. Autorizar o domínio do GitHub Pages
Depois de publicar no GitHub, abra `Authentication` > `Settings` > `Authorized domains` e adicione o domínio do site, por exemplo `SEU_USUARIO.github.io` (sem `https://` e sem caminho do repositório). `localhost` pode ser usado para testes locais via servidor.

## 5. Publicar no GitHub Pages
1. Crie um repositório **público** no GitHub, por exemplo `meu-financeiro`. O código do site ficará público; os dados financeiros não são armazenados no GitHub.
2. Envie `index.html`, `firebase-config.js` e, opcionalmente, `firestore.rules`/`README.md` para a raiz do repositório.
3. Em `Settings` > `Pages`, escolha `Deploy from a branch`, branch `main` e pasta `/ (root)`, e salve.
4. Aguarde a publicação indicada pelo GitHub Pages e abra o endereço gerado.
5. Cadastre o domínio `SEU_USUARIO.github.io` nos domínios autorizados do Firebase Authentication.

## 6. Primeiro acesso e migração
- Entre com o e-mail e a senha que você criou no Firebase Console.
- Se este navegador tiver dados do site anterior em `localStorage`, o sistema oferecerá copiá-los para a nuvem.
- Se os dados antigos estavam no arquivo aberto como `file://` ou em outro navegador/dispositivo, exporte um backup JSON no site antigo e use `Importar` na nova versão depois de entrar. A importação substitui o conjunto atual de movimentações/orçamentos dessa conta.
- Depois da migração, qualquer inclusão, edição, exclusão, categoria ou orçamento é enviado ao Firestore. As alterações aparecem nos dispositivos conectados.

## Segurança e limites
- A configuração web Firebase contém identificadores públicos do app; a proteção dos dados depende das regras do Firestore e da autenticação. Nunca publique `serviceAccountKey.json`, credenciais privadas ou senhas.
- O projeto foi pensado para o plano gratuito Spark. O Firestore Standard oferece cota sem custo de 1 GiB armazenado, 50 mil leituras/dia, 20 mil gravações/dia, 20 mil exclusões/dia e 10 GiB/mês de transferência de saída. Cotas e condições podem mudar; acompanhe o painel Usage do Firebase.
- Backups gerenciados, PITR e restauração do Firestore exigem faturamento. A exportação JSON do próprio site permanece disponível como cópia opcional.
- Para não gerar cobrança, não habilite o plano Blaze nem adicione uma conta de faturamento. Se a cota gratuita for atingida, operações podem falhar até a renovação da cota.
- GitHub Pages é hospedagem estática; o banco e a autenticação são serviços separados do Firebase.

## SDK
A página importa o SDK modular do Firebase 13.2.0 como módulos ES do CDN oficial `gstatic`. A documentação oficial mostra esse formato de importação para uso no navegador.
