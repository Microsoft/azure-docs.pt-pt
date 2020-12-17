---
title: Tutorial para configurar Azure Ative Directy B2C com Zscaler
titleSuffix: Azure AD B2C
description: Saiba como integrar a autenticação Azure AD B2C com o Zscaler
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 223f8acd6aad7aaf4c37e0b2eae2df882ed2ad1d
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629379"
---
# <a name="tutorial-to-configure-zscaler-private-access-with-azure-active-directory-b2c-for-secure-hybrid-access"></a>Tutorial para configurar acesso privado Zscaler com Azure Ative Directory B2C para acesso híbrido seguro

Neste tutorial, aprenda a integrar a autenticação Azure AD B2C com [zscaler Private Access (ZPA)](https://www.zscaler.com/products/zscaler-private-access). A ZPA oferece acesso seguro e baseado em políticas a aplicações e ativos privados sem o custo, a ações ou riscos de segurança de uma Rede Privada Virtual (VPN). A oferta de acesso híbrido seguro da Zscaler permite uma superfície de ataque zero para aplicações viradas para o consumidor quando combinada com Azure AD B2C.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, vai precisar de:

- Uma subscrição do Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)

- [Um inquilino Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) que está ligado à sua assinatura Azure.

- [Assinatura ZPA](https://azuremarketplace.microsoft.com/marketplace/apps/aad.zscalerprivateaccess?tab=Overview)

## <a name="scenario-description"></a>Descrição do cenário

A integração ZPA inclui os seguintes componentes:

- Azure AD B2C - O fornecedor de identidade (IdP) responsável pela verificação das credenciais do utilizador. É também responsável pela inscrição de um novo utilizador.

- ZPA - O serviço responsável pela garantia da aplicação web, impondo [acesso de confiança zero](https://www.microsoft.com/security/blog/2018/12/17/zero-trust-part-1-identity-and-access-management/#:~:text=Azure%20Active%20Directory%20%28Azure%20AD%29%20provides%20the%20strong%2C,to%20express%20their%20access%20requirements%20in%20simple%20terms.).

- Aplicação web - Acolhe o serviço a que o utilizador está a tentar aceder.

O diagrama seguinte mostra como a ZPA se integra com o Azure AD B2C.

![Imagem mostra o diagrama de arquitetura zscaler](media/partner-zscaler/zscaler-architecture-diagram.png)

|Passo | Description |
|:-----| :-----------|
| 1. | O utilizador chega a um Portal do Utilizador ZPA ou a uma aplicação ZPA Browser Access.
| 2. | O ZPA requer informações sobre o contexto do utilizador antes de poder decidir se permite que o utilizador aceda à aplicação web. Para autenticar o utilizador, a ZPA executa um redirecionamento SAML para a página de login Azure AD B2C.  
| 3. | O utilizador chega à página de login do Azure AB B2C. Se for um novo utilizador, o utilizador inscreve-se para criar uma nova conta. Um utilizador existente iniciaria singing usando as suas credenciais existentes. Azure AD B2C valida a identidade do utilizador.
| 4. | Após a autenticação bem sucedida, o Azure AD B2C redireciona o utilizador de volta para zpa juntamente com a afirmação DE SAML. A ZPA verifica a afirmação DOL e define o contexto do utilizador.
| 5. | A ZPA avalia as políticas de acesso ao utilizador. Se o utilizador tiver permissão para aceder à aplicação web, então a ligação é permitida a passagem.

## <a name="onboard-to-zpa"></a>A bordo da ZPA

Este tutorial pressupõe que já tem uma configuração de ZPA. Se estiver a começar com o ZPA, consulte o [guia de configuração passo a passo para zpa](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa).

## <a name="integrate-with-azure-ad-b2c"></a>Integrar com o Azure AD B2C

### <a name="part-1---configure-azure-ad-b2c-as-an-idp-on-zpa"></a>Parte 1 - Configure Azure AD B2C como idP em ZPA

Para configurar a Azure AD B2C como fornecedor de [identidade (IdP) na ZPA:](https://help.zscaler.com/zpa/configuring-idp-single-sign)

1. Inicie sessão no [Portal de Administração ZPA](https://admin.private.zscaler.com)

2. Ir para a  >  **Configuração IdP da** administração

3. **Selecione Adicionar Configuração IdP**

4. Para **1 Informação IdP,** insira o seguinte:

   a. **Nome**: Azure AD B2C

   b. **Único sinal :** Selecione Utilizador

   c. **Domínios**: Escolha os domínios de autenticação que pretende associar a este IdP e, em seguida, selecione **Fazer**

   ![A imagem mostra a informação do IDP](media/partner-zscaler/add-idp-configuration.png)

5. Selecione **Seguinte**

6. Para **2 metadados SP:**

   a. Copie o URL do Fornecedor de Serviços e note-o para posterior utilização.

   b. Copie o ID da Entidade fornecedora de serviços e note-o para posterior utilização.

   ![A imagem mostra a informação dos metadados do Sp](media/partner-zscaler/sp-metadata.png)

7. Selecione **Pausa**

O resto da configuração IdP será retomada após configurar Azure AD B2C.

### <a name="part-2---configure-custom-policy-in-azure-ad-b2c"></a>Parte 2 - Configurar a política personalizada em Azure AD B2C

>[!Note]
>Este passo só é necessário se ainda não tiver políticas personalizadas. Se já tiver uma ou mais políticas personalizadas, pode saltar este passo.

O artigo [começa com políticas personalizadas no Azure Ative Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started) fornece instruções sobre como configurar políticas personalizadas no seu inquilino Azure AD B2C.

### <a name="part-3---register-zpa-as-a-saml-application-in-azure-ad-b2c"></a>Parte 3 - Registar a ZPA como uma aplicação SAML em Azure AD B2C

O artigo [regista um pedido DE SAML em Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers) fornece instruções sobre como configurar um pedido SAML em Azure AD B2C. No [passo 3.2,](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers#32-upload-and-test-your-policy-metadata)será fornecido com o URL de metadados IdP SAML utilizado pelo Azure AD B2C. Vai precisar disto para ser usado mais tarde.

Siga as instruções através do [passo 4.2](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers#42-update-the-app-manifest). No passo 4.2 da instrução, é obrigado a atualizar o manifesto da aplicação. Atualizar as propriedades da seguinte forma:

- **identifierUris**: Utilize o ID da Entidade fornecedora de serviços indicado na **Parte 1, Passo 6a**.

- **samlMetadataUrl**: Ignore esta propriedade uma vez que a ZPA não hospeda um URL de metadados SAML.

- **respostaUrlsWithType**: Utilize o URL do fornecedor de serviços indicado na **Parte 1, Passo 6b**.

- **logoutUrl**: Ignore esta propriedade uma vez que a ZPA não suporta um URL logout.

Os restantes passos não são relevantes para este tutorial.

### <a name="part-4---extract-the-idp-saml-metadata-from-azure-ad-b2c"></a>Parte 4 - Extrair os metadados IdP SAML do Azure AD B2C

A partir do passo anterior, você precisa obter um URL de metadados SAML no seguinte formato:

```https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata```

onde `<tenant-name>` é o nome do seu inquilino Azure AD B2C e é o nome da política personalizada `<policy-name>` saml que criou no último passo.

Por exemplo, https://safemarch.b2clogin.com/safemarch.onmicrosoft.com/B2C_1A_signup_signin_saml//Samlp/metadata "

Abra um navegador web e navegue para o URL de metadados SAML. Quando a página estiver carregada, clique com o botão direito em qualquer lugar da página. Selecione **'Guardar página como'** e guarde o ficheiro no seu computador; Vais usar isto na próxima parte.

### <a name="part-5---complete-idp-configuration-on-zpa"></a>Parte 5 - Configuração completa do IdP no ZPA

Complete a [configuração IdP no Portal de Administração ZPA](https://help.zscaler.com/zpa/configuring-idp-single-sign) que foi parcialmente configurado na Parte 1:

1. Inicie sessão no [Portal de Administração ZPA](https://admin.private.zscaler.com)

2. Vá para a  >  **Configuração IdP da administração**.

3. Selecione o IdP que foi configurado na Parte 1 e selecione **Resume**.

4. Para **3 Criar IdP**

   a. Vá ao Ficheiro de Seleção **de Metadados IdP**  >   e carre fique com o ficheiro metadados que guardou na Parte 4.

   b. Verifique se o **estado** da configuração IdP está **ativado**.

   c. Selecione **Guardar**.

      ![A imagem mostra a criação de informação idp](media/partner-zscaler/create-idp.png)

## <a name="test-the-solution"></a>Testar a solução

Aceda a um Portal do Utilizador ZPA ou a uma aplicação de Acesso ao Navegador e teste o processo de inscrição ou inscrição. Deverá resultar numa autenticação SAML bem sucedida.

## <a name="next-steps"></a>Passos seguintes

Para obter informações adicionais, reveja os seguintes artigos:

- [Começar com políticas personalizadas no Azure Ative Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)

- [Registar um pedido DE SAML em Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers)

- [Guia de configuração passo a passo para zpa](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)

- [Configurar um IdP para um único sign-on](https://help.zscaler.com/zpa/configuring-idp-single-sign)
