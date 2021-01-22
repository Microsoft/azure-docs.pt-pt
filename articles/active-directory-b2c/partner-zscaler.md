---
title: Tutorial - Configurar Azure Ative Directy B2C com Zscaler
titleSuffix: Azure AD B2C
description: Saiba como integrar a autenticação Azure AD B2C com o Zscaler.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: fe427150b15c6bccb97172ae751235d388c95c7b
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98675031"
---
# <a name="tutorial-configure-zscaler-private-access-with-azure-active-directory-b2c"></a>Tutorial: Configurar acesso privado Zscaler com Diretório Ativo Azure B2C

Neste tutorial, você aprenderá a integrar a autenticação do Azure Ative Directory B2C (Azure AD B2C) com [zscaler Private Access (ZPA)](https://www.zscaler.com/products/zscaler-private-access). A ZPA oferece acesso seguro e baseado em políticas a aplicações e ativos privados sem o custo, a ações ou riscos de segurança de uma rede privada virtual (VPN). A oferta de acesso híbrido seguro Zscaler permite uma superfície de ataque zero para aplicações viradas para o consumidor quando é combinada com Azure AD B2C.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, vai precisar de:

- Uma subscrição do Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)  
- [Um inquilino Azure AD B2C](./tutorial-create-tenant.md) que está ligado à sua assinatura Azure.  
- [Uma assinatura ZPA](https://azuremarketplace.microsoft.com/marketplace/apps/aad.zscalerprivateaccess?tab=Overview).

## <a name="scenario-description"></a>Descrição do cenário

A integração ZPA inclui os seguintes componentes:

- **Azure AD B2C**: O fornecedor de identidade (IdP) responsável pela verificação das credenciais do utilizador. É também responsável pela inscrição de um novo utilizador.  
- **ZPA**: O serviço responsável pela garantia da aplicação web, impondo [acesso de confiança zero](https://www.microsoft.com/security/blog/2018/12/17/zero-trust-part-1-identity-and-access-management/#:~:text=Azure%20Active%20Directory%20%28Azure%20AD%29%20provides%20the%20strong%2C,to%20express%20their%20access%20requirements%20in%20simple%20terms.).  
- **A aplicação web**: Acolhe o serviço a que o utilizador está a tentar aceder.

O diagrama seguinte mostra como a ZPA se integra com o Azure AD B2C.

![Diagrama da arquitetura Zscaler, mostrando como a ZPA se integra com a Azure AD B2C.](media/partner-zscaler/zscaler-architecture-diagram.png)

A sequência é descrita na tabela seguinte:

|Passo | Descrição |
| :-----:| :-----------|
| 1 | Um utilizador chega a um portal de utilizador ZPA ou a uma aplicação de acesso ao navegador ZPA.
| 2 | O ZPA requer informações sobre o contexto do utilizador antes de poder decidir se permite que o utilizador aceda à aplicação web. Para autenticar o utilizador, a ZPA executa um redirecionamento SAML para a página de login Azure AD B2C.  
| 3 | O utilizador chega à página de login do Azure AB B2C. Os novos utilizadores inscrevem-se para criar uma conta e os utilizadores existentes iniciam sessão com as suas credenciais existentes. O Azure AD B2C valida a identidade do utilizador.
| 4 | Após a autenticação bem sucedida, o Azure AD B2C redireciona o utilizador de volta para zpa juntamente com a afirmação DE SAML. A ZPA verifica a afirmação DOL e define o contexto do utilizador.
| 5 | A ZPA avalia as políticas de acesso ao utilizador. Se o utilizador tiver permissão para aceder à aplicação web, a ligação pode passar.

## <a name="onboard-to-zpa"></a>A bordo da ZPA

Este tutorial pressupõe que já tem uma configuração ZPA em funcionamento. Se estiver a começar com o ZPA, consulte o [guia de configuração passo a passo para zpa](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa).

## <a name="integrate-zpa-with-azure-ad-b2c"></a>Integre o ZPA com o Azure AD B2C

### <a name="step-1-configure-azure-ad-b2c-as-an-idp-on-zpa"></a>Passo 1: Configurar Azure AD B2C como idP em ZPA

Para configurar o Azure AD B2C como [idP na ZPA,](https://help.zscaler.com/zpa/configuring-idp-single-sign)faça o seguinte:

1. Faça login no [Portal de Administração ZPA](https://admin.private.zscaler.com).

1. Vá para a  >  **Configuração IdP da administração**.

1. **Selecione Adicionar Configuração IdP**.

   Abre-se o painel de **configuração Add IdP.**

   ![Screenshot do separador "Informação IdP" no painel "Adicionar configuração IdP".](media/partner-zscaler/add-idp-configuration.png)

1. Selecione o separador **informações IdP** e, em seguida, faça o seguinte:

   a. Na caixa **Nome,** insira **Azure AD B2C**.  
   b. Em **'S'A's Sign-On** único , selecione **Utilizador**.  
   c. Na lista de down-down **do Domínios,** selecione os domínios de autenticação que pretende associar a este IdP.

1. Selecione **Seguinte**.

1. Selecione o separador **MEtadados SP** e, em seguida, faça o seguinte:

   a. Em **URL do Fornecedor de Serviços,** copie ou note o valor para posterior utilização.  
   b. No **ID da Entidade fornecedora de serviços,** copie ou note o valor para posterior utilização.

   ![Screenshot do separador "MEtadados SP" no painel "Adicionar configuração IdP".](media/partner-zscaler/sp-metadata.png)

1. Selecione **Pausa**.

Depois de configurar Azure AD B2C, o resto da configuração IdP retoma.

### <a name="step-2-configure-custom-policies-in-azure-ad-b2c"></a>Passo 2: Configurar políticas personalizadas em Azure AD B2C

>[!Note]
>Este passo só é necessário se ainda não tiver configurado políticas personalizadas. Se já tiver uma ou mais políticas personalizadas, pode saltar este passo.

Para configurar políticas personalizadas no seu inquilino Azure AD B2C, consulte [Começar com políticas personalizadas no Azure Ative Directory B2C](./custom-policy-get-started.md).

### <a name="step-3-register-zpa-as-a-saml-application-in-azure-ad-b2c"></a>Passo 3: Registar a ZPA como uma aplicação SAML em Azure AD B2C

Para configurar uma aplicação SAML em Azure AD B2C, consulte [Registar uma aplicação SAML em Azure AD B2C](./connect-with-saml-service-providers.md). 

No passo ["3.2 Carregar e testar os seus metadados de política",](./connect-with-saml-service-providers.md#32-upload-and-test-your-policy-metadata)copiar ou observar o URL de metadados IdP SAML que é utilizado pelo Azure AD B2C. Precisará dela mais tarde.

Siga as instruções através do passo ["4.2 Atualizar o manifesto da aplicação".](./connect-with-saml-service-providers.md#42-update-the-app-manifest) No passo 4.2, atualize as propriedades manifesto da aplicação da seguinte forma:

- Para **o identificadorUris**: Utilize o ID da Entidade fornecedora de serviços que copiou ou anotado anteriormente em "Passo 1.6.b".  
- Para **samlMetadataUrl**: Ignore esta propriedade, porque a ZPA não hospeda um URL de metadados SAML.  
- Para **respostaUrlsWithType**: Utilize o URL do fornecedor de serviços que copiou ou anotava anteriormente em "Passo 1.6.a".  
- Para **logoutUrl**: Ignore esta propriedade, porque a ZPA não suporta um URL logout.

O resto dos passos não são relevantes para este tutorial.

### <a name="step-4-extract-the-idp-saml-metadata-from-azure-ad-b2c"></a>Passo 4: Extrair os metadados IdP SAML do Azure AD B2C

Em seguida, precisa de obter um URL de metadados SAML no seguinte formato:

```https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata```

Note que `<tenant-name>` é o nome do seu inquilino Azure AD B2C, e é o nome da política personalizada da `<policy-name>` SAML que criou no passo anterior.

Por exemplo, o URL pode ser `https://safemarch.b2clogin.com/safemarch.onmicrosoft.com/B2C_1A_signup_signin_saml//Samlp/metadata` .

Abra um navegador web e vá para o URL de metadados SAML. Clique com o botão direito em qualquer lugar da página, **selecione Guardar como** e, em seguida, guarde o ficheiro para o seu computador para ser utilizado no passo seguinte.

### <a name="step-5-complete-the-idp-configuration-on-zpa"></a>Passo 5: Complete a configuração IdP no ZPA

Complete a [configuração IdP no Portal de Administração ZPA](https://help.zscaler.com/zpa/configuring-idp-single-sign) que configura parcialmente em "Passo 1: Configurar Azure AD B2C como IdP em ZPA".

1. No [Portal ZPA Admin,](https://admin.private.zscaler.com)aceda à   >  **Configuração IdP da Administração**.

1. Selecione o IdP que configurar em "Passo 1", e, em seguida, **selecione Resume**.

1. No painel **de configuração do Add IdP,** selecione o **separador Criar IdP** e, em seguida, faça o seguinte:

   a. No **Ficheiro de Metadados IdP,** faça o upload do ficheiro de metadados que guardou anteriormente em "Passo 4: Extrair os metadados IdP SAML do Azure AD B2C".  
   b. Verifique se o **Estado** da configuração IdP está **Ativado**.  
   c. Selecione **Guardar**.

   ![Screenshot do separador "Criar IdP" no painel "Adicionar configuração IdP".](media/partner-zscaler/create-idp.png)

## <a name="test-the-solution"></a>Testar a solução

Aceda a um portal de utilizador ZPA ou a uma aplicação de acesso ao navegador e teste o processo de inscrição ou inscrição. O teste deverá resultar numa autenticação SAML bem sucedida.

## <a name="next-steps"></a>Passos seguintes

Para mais informações, reveja os seguintes artigos:

- [Começar com políticas personalizadas em Azure AD B2C](./custom-policy-get-started.md)
- [Registar um pedido DE SAML em Azure AD B2C](./connect-with-saml-service-providers.md)
- [Guia de configuração passo a passo para zpa](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)
- [Configure um IdP para um único sign-on](https://help.zscaler.com/zpa/configuring-idp-single-sign)