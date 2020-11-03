---
title: Tutorial para configurar identidade N8 com diretório ativo Azure B2C
titleSuffix: Azure AD B2C
description: Tutorial para configurar a Ferramenta de Administração do TheAccessHub com o Azure Ative Directory B2C para abordar a migração de contas de clientes e a administração de Pedidos de Atendimento ao Cliente (CSR).
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/26/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: a6d6ca825a556ea3c98fb94d4becbb75b8f2a7d7
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93294325"
---
# <a name="tutorial-for-configuring-theaccesshub-admin-tool-with-azure-active-directory-b2c"></a>Tutorial para configurar a ferramenta de administração DoAccessHub com diretório ativo Azure B2C

Neste tutorial de amostra, fornecemos orientações sobre como integrar o Azure Ative Directory (AD) B2C com [a Ferramenta de Administração TheAccessHub](https://n8id.com/products/theaccesshub-admintool/) da Identidade N8. Esta solução aborda a migração de contas de clientes e a administração de Pedidos de Atendimento ao Cliente (RSE).  

Esta solução é adequada para si, se tiver uma ou mais das seguintes necessidades:

- Tem um site existente e quer migrar para Azure AD B2C. No entanto, está a ter dificuldades com a migração das suas contas de clientes, incluindo senhas

- Necessita de uma ferramenta para o seu CSR administrar contas Azure AD B2C.

- Tem a obrigação de utilizar a administração delegada para as suas RSE.

- Pretende sincronizar e fundir os seus dados de muitos repositórios em Azure AD B2C.

## <a name="pre-requisites"></a>Pré-requisitos

Para começar, vai precisar de:

- Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)

- Um [inquilino Azure AD B2C.](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) O inquilino deve estar ligado à sua assinatura Azure.

- Um ambiente de ferramentas admin Do TheAccessHub: Contacte [a Identidade N8](https://n8id.com/contact/) para providenciar um novo ambiente.

- [Opcional] Informações de ligação e credenciais para quaisquer bases de dados ou Protocolos de Acesso ao Diretório Leve (LDAPs) de que pretende migrar os dados dos clientes.

- [Opcional] Ambiente Azure AD B2C configurado para a utilização de [políticas personalizadas,](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)se pretender integrar a Ferramenta de Administração DoAccessHub no seu fluxo de política de inscrição.

## <a name="scenario-description"></a>Descrição do cenário

A Ferramenta Admin TheAccessHub funciona como qualquer outra aplicação em Azure. Pode funcionar na subscrição Azure da N8 Identity ou na subscrição do cliente. O seguinte diagrama de arquitetura mostra a implementação.

![Imagem mostrando diagrama de arquitetura n8identity](./media/partner-n8identity/n8identity-architecture-diagram.png)

|Passo | Description |
|:-----| :-----------|
| 1. | O utilizador chega a uma página de login. Os utilizadores selecionam o sesurá-se para criar uma nova conta e introduzir informações na página. Azure AD B2C recolhe os atributos do utilizador.
| 2. | Azure AD B2C chama a Ferramenta de Administração TheAccessHub e passa nos atributos do utilizador
| 3. | A Ferramenta Admin DoAccessHub verifica a base de dados existente para obter informações atuais do utilizador.  
| 4. | O registo do utilizador é sincronizado da base de dados para a Ferramenta Admin TheAccessHub.
| 5. | A Ferramenta Admin DoAccessHub partilha os dados com a administração delegada do CSR/helpdesk.
| 6. | A Ferramenta Admin DoAccessHub sincroniza os registos do utilizador com o Azure AD B2C.
| 7. |Com base na resposta de sucesso/falha da Ferramenta Admin TheAccessHub, a Azure AD B2C envia um e-mail de boas-vindas personalizado ao utilizador.

## <a name="create-a-global-admin-in-your-azure-ad-b2c-tenant"></a>Crie um Administrador Global no seu inquilino Azure AD B2C

A Ferramenta Admin TheAccessHub requer permissões para agir em nome de um Administrador Global para ler informações do utilizador e realizar alterações no seu inquilino Azure AD B2C. As alterações aos seus administradores regulares ganharam; t impactar a capacidade da Ferramenta Admin DoAccessHub de interagir com o inquilino.

Para criar um Administrador Global, siga estes passos:

1. No portal Azure, inscreva-se no seu inquilino Azure AD B2C como administrador. Navegue para utilizadores **de diretório ativo Azure**  >  **Users**
2. Selecione **Novo Utilizador**
3. Escolha **criar o Utilizador** para criar um utilizador de diretório regular e não um cliente
4. Preencha o formulário de informação de identidade

   a. Insira o nome de utilizador como 'theaccesshub'

   b. Insira o nome como 'TheAccessHub Service Account'

5. Selecione **Mostrar Palavra-Passe** e copie a palavra-passe inicial para posterior utilização
6. Atribuir a função de Administrador Global

   a. Selecione as funções atuais do **utilizador Utilizador** para alterá-lo

   b. Verifique o registo do Administrador Global

   c. **Selecione**  >  **Criar**

## <a name="connect-theaccesshub-admin-tool-with-your-azure-ad-b2c-tenant"></a>Ligue a ferramenta de administração DoAccessHub com o seu inquilino Azure AD B2C

A Ferramenta Admin DoAccessHub utiliza a API de Gráfico da Microsoft para ler e fazer alterações no seu diretório. Atua como administrador global no seu inquilino. A Ferramenta Admin TheAccessHub, que pode conceder a partir da ferramenta.

Para autorizar a Ferramenta de Administração TheAccessHub a aceder ao seu diretório, siga estes passos:

1. Inicie sessão na Ferramenta de Administração TheAccessHub utilizando credenciais fornecidas por N8 Identity

2. Navegue para **o Sistema Admin**  >  **Azure AD B2C Config**

3. **Selecione a Ligação de Autor**

4. No novo teste de arranque da janela com a sua conta de Administrador Global. Pode ser-lhe pedido que reponha a sua palavra-passe se estiver a iniciar sessão pela primeira vez com a nova conta de serviço.

5. Siga as instruções e **selecione Aceite** conceder à Ferramenta de Administração TheAccessHub as permissões solicitadas.

## <a name="configure-a-new-csrhelpdesk-user-using-your-enterprise-identity"></a>Configure um novo utilizador CSR/Helpdesk usando a sua identidade da empresa

Crie um utilizador CSR/Helpdesk que aceda à Ferramenta Admin TheAccessHub utilizando as credenciais de Diretório Ativo da empresa existente.

Para configurar o utilizador CSR/Helpdesk com um único sign-on (SSO), recomenda-se os seguintes passos:

1. Inicie sessão na Ferramenta de Administração DoAccessHub utilizando credenciais fornecidas pela N8 Identity.

2. Navegue para **o Gestor De Ferramentas**  >  **Gerir Colegas**

3. Selecione **Adicionar Colega**

4. Selecione **colega tipo administrador Azure**

5. Insira as informações de perfil do colega

   a. A escolha de uma Organização Domiciliária controlará quem tem permissão para gerir este utilizador.

   b. Para o nome de utilizador do Login ID/Azure AD, forneça o Nome Principal do Utilizador da conta Azure Ative Directory do utilizador.

   c. No separador TheAccessHub Roles, verifique a função gerida Helpdesk. Permitirá ao utilizador aceder à visão dos colegas de gestão. O utilizador ainda terá de ser colocado num grupo ou ser feito proprietário de uma organização para agir sobre os clientes.

6. Selecione **Submeter**.

## <a name="configure-a-new-csrhelpdesk-user-using-a-new-identity"></a>Configure um novo utilizador de CSR/Helpdesk usando uma nova identidade

Crie um utilizador CSR/Helpdesk que aceda à Ferramenta de Administração TheAccessHub com uma nova credencial local única para a Ferramenta Admin TheAccessHub. Isto será usado principalmente por organizações que não usam um AD Azure para a sua empresa.

Para configurar um utilizador [CSR/Helpdesk](https://youtu.be/iOpOI2OpnLI) sem SSO, siga estes passos:

1. Inicie sessão na Ferramenta de Administração DoAccessHub utilizando credenciais fornecidas pela N8 Identity

2. Navegue para **o Gestor De Ferramentas**  >  **Gerir Colegas**

3. Selecione **Adicionar Colega**

4. Selecione **Administrador Local tipo colega**

5. Insira as informações de perfil do colega

   a. A escolha de uma Organização Domiciliária controlará quem tem permissão para gerir este utilizador.

   b. No **separador TheAccessHub Roles,** selecione a função gerida **Helpdesk**. Permitirá ao utilizador aceder à visão dos colegas de gestão. O utilizador ainda terá de ser colocado num grupo ou ser feito proprietário de uma organização para agir sobre os clientes.

6. Copie os **atributos de ID/Email de login** e **password de uma vez.** Forneça-o ao novo utilizador. Eles vão usar estas credenciais para iniciar sessão na Ferramenta Admin TheAccessHub. O utilizador será solicitado a introduzir uma nova palavra-passe no seu primeiro login.

7. Selecione **Submeter**

## <a name="configure-partitioned-csrhelpdesk-administration"></a>Configure a administração de RSE/Helpdesk dividida

As permissões para gerir utilizadores de clientes e CSR/Helpdesk na Ferramenta Admin TheAccessHub são geridas com o uso de uma hierarquia de organização. Todos os colegas e clientes têm uma organização doméstica onde residem. Colegas ou grupos específicos de colegas podem ser designados como proprietários de organizações.  Os proprietários de organizações podem gerir (fazer alterações a) colegas e clientes em organizações ou suborganizações que possuam. Para permitir que vários colegas possam gerir um conjunto de utilizadores, um grupo pode ser criado com muitos membros. O grupo pode então ser designado como proprietário de uma organização e todos os membros do grupo podem gerir colegas e clientes na organização.

### <a name="create-a-new-group"></a>Criar um novo grupo

1. Inicie sessão na Ferramenta de Administração TheAccessHub utilizando **credenciais fornecidas** por N8 Identity

2. Navegar para **grupos de gestão de > de organização**

3. Selecione > **Adicionar Grupo**

4. Introduza um **nome de grupo** , **descrição do grupo** e dono do **grupo**

5. Procure e verifique as caixas nos colegas que pretende ser membros do grupo e, em seguida, selecione > **Adicionar**

6. Na parte inferior da página, pode ver todos os membros do grupo.

7. Se necessário, os membros podem ser removidos selecionando o **x** no final da linha

8. Selecione **Submeter**

### <a name="create-a-new-organization"></a>Criar uma nova organização

1. Inicie sessão na Ferramenta de Administração TheAccessHub utilizando credenciais fornecidas por N8 Identity

2. Navegar para organizações > **gerir organizações**

3. Selecione > **Adicionar Organização**

4. Forneça um **nome de Organização,** **proprietário da organização** e organização dos **pais.**

    a. O nome da organização é idealmente um valor que corresponde aos dados do seu cliente. Ao carregar dados de colegas e clientes, se fornecer o nome da organização na carga, o colega pode ser automaticamente colocado na organização.

    b. O proprietário representa a pessoa ou grupo que irá gerir os clientes e colegas desta organização e qualquer suborganização dentro.

    c. A organização-mãe indica qual outra organização é inerentemente, também responsável por esta organização.

5. Selecione **Submeter**.

### <a name="modify-the-hierarchy-via-the-tree-view"></a>Modifique a hierarquia através da vista da árvore

1. Inicie sessão na Ferramenta de Administração TheAccessHub utilizando credenciais fornecidas por N8 Identity

2. Navegue para **a vista de ferramentas de**  >  **gerente**

3. Nesta representação, pode visualizar quais colegas e grupos podem gerir quais as organizações.

4. As hierarquias podem ser modificadas arrastando organizações que querem que sejam parentadas.

5. **Selecione Guarde** quando terminar de alterar a hierarquia.

## <a name="customize-welcome-notification"></a>Personalizar notificação de boas-vindas

Enquanto estiver a utilizar o TheAccessHub para migrar os utilizadores de uma solução de autenticação anterior para a Azure AD B2C, poderá querer personalizar a notificação de boas-vindas do utilizador, que é enviada ao utilizador pelo TheAccessHub durante a migração. Esta mensagem normalmente inclui o link para o cliente definir uma nova palavra-passe no diretório Azure AD B2C.

Para personalizar a notificação:

1. Inicie sessão no TheAccessHub utilizando credenciais fornecidas por N8 Identity

2. Navegar para notificações **de administrador**  >  **de** sistema

3. Selecione o **modelo criar colega**

4. Selecione **Editar**

5. Altere os campos de mensagem e modelo conforme necessário. O campo de modelo está ciente de HTML e pode enviar notificações formatadas html para e-mails do cliente.

6. **Selecione Guardar** quando terminar.

## <a name="migrate-data-from-external-data-sources-to-azure-ad-b2c"></a>Migrar dados de fontes de dados externas para Azure AD B2C

Utilizando a Ferramenta Admin TheAccessHub, pode importar dados de várias bases de dados, ficheiros LDAPs e CSV e, em seguida, empurrar esses dados para o seu inquilino Azure AD B2C. É feito carregando dados para o tipo de colega de utilizador Azure AD B2C dentro da Ferramenta Admin TheAccessHub.  Se a fonte de dados não for a própria Azure, os dados serão colocados tanto na Ferramenta Admin TheAccessHub como no Azure AD B2C. Se a origem dos seus dados externos não for um simples ficheiro .csv na sua máquina, crie uma fonte de dados antes de escovou a carga de dados. Os passos abaixo descrevem a criação de uma fonte de dados e a carga de dados.

### <a name="configure-a-new-data-source"></a>Configurar uma nova fonte de dados

1. Inicie sessão na Ferramenta de Administração TheAccessHub utilizando credenciais fornecidas por N8 Identity

2. Navegar para **fontes de**  >  **dados** de administrador de sistema

3. Selecione **Adicionar Fonte de Dados**

4. Forneça um **Nome** e **Tipo** para esta fonte de dados

5. Preencha os dados do formulário, dependendo do seu tipo de fonte de dados:

   **Para bases de dados**

   a. **Tipo** – Base de Dados

   b. **Tipo de base de dados** – Selecione uma base de dados de um dos tipos de base de dados suportados.

   c. **URL de ligação** – Introduza uma cadeia de ligação JDBC bem formatada. Tais como: ``jdbc:postgresql://myhost.com:5432/databasename``

   d. **Nome de utilizador** – Introduza o nome de utilizador para aceder à base de dados

   e. **Palavra-passe** – Introduza a palavra-passe para aceder à base de dados

   f. **Consulta** – Introduza a consulta SQL para extrair os detalhes do cliente. Tais como: ``SELECT * FROM mytable;``

   exemplo, Selecione **a Ligação de Teste,** verá uma amostra dos seus dados para garantir que a ligação está a funcionar.

   **Para LDAPs**

   a. **Tipo** – LDAP

   b. **Anfitrião** – Introduza o nome de anfitrião ou IP para a máquina em que o servidor LDAP está em funcionamento. Tais como: ``mysite.com``

   c. **Porta** – Introduza o número de porta no qual o servidor LDAP está a ouvir.

   d. **SSL** – Verifique a caixa se a Ferramenta Admin DoAccessHub deve comunicar com o LDAP de forma segura utilizando sSL. A utilização de SSL é altamente recomendada.

   e. **Iniciar sessão DN** – Insira o DN da conta de utilizador para iniciar sessão e faça a pesquisa LDAP

   f. **Palavra-passe** – Introduza a palavra-passe para o utilizador

   exemplo, **Base DN** – Insira o DN no topo da hierarquia em que pretende fazer a pesquisa

   h. **Filtro** – Introduza a cadeia de filtro LDAP, que obterá os registos do seu cliente

   i. **Atributos** – Introduza uma lista de atributos separados em vírgula dos registos dos seus clientes para passar para a Ferramenta de Administração DoAccessHub

   j. Selecione a **Ligação de Teste,** verá uma amostra dos seus dados para garantir que a ligação está a funcionar.

   **Para OneDrive**

   a. **Tipo** – OneDrive para Negócios

   b. **Selecione a Ligação de Autor**

   c. Uma nova janela irá instru-lo a iniciar sessão no **OneDrive,** iniciar sessão com um utilizador com acesso lido à sua conta OneDrive. A Ferramenta De Administração DoAccessHub atuará para que este utilizador leia ficheiros de carga CSV.

   d. Siga as instruções e **selecione Aceite** conceder à Ferramenta de Administração TheAccessHub as permissões solicitadas.

6. **Selecione Guardar** quando terminar.  

### <a name="synchronize-data-from-your-data-source-into-azure-ad-b2c"></a>Sincronizar os dados da sua fonte de dados para o Azure AD B2C

1. Inicie sessão na Ferramenta de Administração TheAccessHub utilizando credenciais fornecidas por N8 Identity

2. Navegar para a sincronização **de**  >  **dados do** administrador do sistema

3. Selecione **Nova Carga**

4. Selecione o Utilizador Azure AD B2C **do Tipo Colega**

5. Selecione **Source** , no diálogo pop-up, selecione a sua fonte de dados. Se criou uma fonte de dados OneDrive, selecione também o ficheiro.

6. Se não quiser criar novas contas de clientes com esta carga, então altere a primeira política: **SE colega não encontrado no TheAccessHub THEN** to Do **Nothing**

7. Se não pretender atualizar as contas de clientes existentes com esta carga, em seguida, altere a segunda política **IF source e TheAccessHub desajuste de dados THEN** to Do **Nothing**

8. Selecione **Seguinte**

9. Na **configuração search-Mapping,** identificamos como correlacionar registos de carga com clientes já carregados na Ferramenta Admin TheAccessHub. Escolha um ou mais atributos de identificação na fonte. Combine os atributos com um atributo na Ferramenta Admin TheAccessHub que detém os mesmos valores. Se for encontrado um fósforo, então o registo existente será ultrapassado; caso contrário, será criado um novo cliente. Pode sequenciar uma série destes cheques. Por exemplo, primeiro pode verificar o e-mail e, em seguida, primeiro e último nome.

10. No menu do lado esquerdo, selecione **Data Mapping**.

11. Na configuração Data-Mapping, atribua quais os atributos da Ferramenta de Administração TheAccessHub devem ser povoados dos seus atributos de origem. Não há necessidade de mapear todos os atributos. Os atributos não mapeados permanecerão inalterados para os clientes existentes.

12. Se mapear o atributo org_name com um valor que é o nome de uma organização existente, então novos clientes criados serão colocados nessa organização.

13. Selecione **Seguinte**

14. Um horário diário/semanal ou mensal pode ser especificado se esta carga deve voltar a ocorrer. Caso contrário, mantenha o padrão **Agora**.

15. Selecione **Submeter**

16. Se a **programação Now** foi selecionada, um novo registo será adicionado imediatamente ao ecrã de Sincronizações de Dados. Uma vez que a fase de validação tenha atingido os 100%, selecione o **novo recorde** para ver o resultado esperado para a carga. Para as cargas programadas, estes registos só aparecerão após a hora marcada.

17. Se não houver erros, selecione **Executar** para cometer as alterações. Caso contrário, **selecione Remover** do menu **Mais** para remover a carga. Em seguida, pode corrigir os dados de origem ou carregar mapeamentos e tentar novamente. Em vez disso, se o número de erros for pequeno, pode atualizar manualmente os registos e selecionar **Update** em cada registo para escamar correções. Finalmente, pode continuar com quaisquer erros e resolvê-los mais tarde como **Intervenções de Apoio** na Ferramenta Administrador DoAccessHub.

18. Quando o registo **de sincronização de dados** se tornar 100% na fase de carga, todas as alterações resultantes da carga terão sido iniciadas. Os clientes devem começar a aparecer ou a receber alterações no Azure AD B2C.

## <a name="synchronize-azure-ad-b2c-customer-data-into-theaccesshub-admin-tool"></a>Synchronize os dados dos clientes do Azure AD B2C na ferramenta de administração TheAccessHub

Como uma operação única ou em curso, a Ferramenta Admin TheAccessHub pode sincronizar todas as informações do cliente de Azure AD B2C para a Ferramenta Admin TheAccessHub. Isto garante que os administradores da CSR/Helpdesk estão a ver informações atualizadas do cliente.

Para sincronizar os dados do Azure AD B2C na ferramenta Admin TheAccessHub:

1. Inicie sessão na Ferramenta de Administração TheAccessHub utilizando credenciais fornecidas por N8 Identity

2. Navegar para a sincronização **de**  >  **dados do** administrador do sistema

3. Selecione **Nova Carga**

4. Selecione o Utilizador Azure AD B2C **do Tipo Colega**

5. Para o passo **Opções,** deixe as predefinições.

6. Selecione **Seguinte**

7. Para o **passo de pesquisa & de mapeamento de dados,** deixe as predefinições. Exceto se mapear o atributo **org_name** com um valor que é o nome de uma organização existente, então novos clientes criados serão colocados nessa organização.

8. Selecione **Seguinte**

9. Um horário diário/semanal ou mensal pode ser especificado se esta carga deve voltar a ocorrer. Caso contrário, mantenha o padrão: **Agora**. Recomendamos sincronização a partir de Azure AD B2C regularmente.

10. Selecione **Submeter**

11. Se a programação **Now** foi selecionada, um novo registo será adicionado imediatamente ao ecrã de Sincronizações de Dados. Uma vez que a fase de validação tenha atingido os 100%, selecione o novo recorde para ver o resultado esperado para a carga. Para as cargas programadas, estes registos só aparecerão após a hora marcada.

12. Se não houver erros, selecione **Executar** para cometer as alterações. Caso contrário, **selecione Remover** do menu Mais para remover a carga. Em seguida, pode corrigir os dados de origem ou carregar mapeamentos e tentar novamente. Em vez disso, se o número de erros for pequeno, pode atualizar manualmente os registos e selecionar **Update** em cada registo para escamar correções. Finalmente, pode continuar com quaisquer erros e resolvê-los mais tarde como Intervenções de Apoio na Ferramenta Administrador DoAccessHub.

13. Quando o registo **de sincronização de dados** se tornar 100% na fase de carga, todas as alterações resultantes da carga terão sido iniciadas.

## <a name="configure-azure-ad-b2c-policies-to-call-theaccesshub-admin-tool"></a>Configure políticas AD B2C para chamar a Ferramenta de Administração TheAccessHub

Ocasionalmente, sincronizar a Ferramenta Admin DoAccessHub é limitada na sua capacidade de manter o seu estado atualizado com a Azure AD B2C. Podemos aproveitar as políticas de API e AZure AD B2C da AD AD AD para informar a Ferramenta de Administração DoAccessHub à medida que acontecem. Esta solução requer conhecimento técnico das [políticas personalizadas Azure AD B2C.](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started#:~:text=%20Get%20started%20with%20custom%20policies%20in%20Azure,Experience%20Framework%20applications.%20Azure%20AD%20B2C...%20More%20) Na secção seguinte, daremos um exemplo de passos de política e um certificado seguro para notificar a Ferramenta de Administração TheAccessHub de novas contas nas suas políticas personalizadas Sign-Up.

### <a name="create-a-secure-credential-to-invoke-theaccesshub-admin-tools-api"></a>Crie uma credencial segura para invocar a API da API da ADmin Tool do TheAccessHub

1. Inicie sessão na Ferramenta de Administração TheAccessHub utilizando credenciais fornecidas por N8 Identity

2. Navegue para **a Segurança** API de  >  **Ferramentas**  >  **AMIN** do Sistema

3. Selecione **Gerar**

4. Copiar a senha do **certificado**

5. Selecione **Download** para obter o certificado de cliente.

6. Siga este [tutorial](https://docs.microsoft.com/azure/active-directory-b2c/secure-rest-api#https-client-certificate-authentication ) para adicionar o certificado de cliente no Azure AD B2C.

### <a name="retrieve-your-custom-policy-examples"></a>Recupere os seus exemplos de política personalizada

1. Inicie sessão na Ferramenta de Administração TheAccessHub utilizando credenciais fornecidas por N8 Identity

2. Navegar para **ferramentas de** administração de administrador de sistema  >  **Admin Tools**  >  **azure B2C Políticas**

3. Forneça o seu domínio de inquilino Azure AD B2C e os dois IDs de enquadramento de experiência de identidade a partir da configuração do Quadro de Experiência de Identidade

4. Selecione **Guardar**

5. Selecione **Download** para obter um ficheiro zip com políticas básicas que adicionam clientes à Ferramenta Admin TheAccessHub à medida que os clientes se inscrevem.

6. Siga este [tutorial](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started) para começar com a conceção de políticas personalizadas em Azure AD B2C.

## <a name="next-steps"></a>Passos seguintes

Para obter informações adicionais, reveja os seguintes artigos:

- [Políticas personalizadas no Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Começar com políticas personalizadas em Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)