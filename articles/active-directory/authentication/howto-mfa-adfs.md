---
title: Recursos seguros com Azure AD MFA e ADFS - Azure Ative Directory
description: Esta é a página de autenticação multi-factor Ad Azure que descreve como começar com Azure AD MFA e AD FS na nuvem.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b42f920726d4b3046ab0c292e1090f5217e8b1f9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96743246"
---
# <a name="securing-cloud-resources-with-azure-ad-multi-factor-authentication-and-ad-fs"></a>Garantir recursos em nuvem com autenticação multi-factor Azure AD e FS AD

Se a sua organização estiver federada com o Azure Ative Directory, utilize o Azure AD Multi-Factor Authentication ou Ative Directory Federation Services (AD FS) para garantir recursos que são acedidos pela Azure AD. Utilize os seguintes procedimentos para garantir os recursos do Azure Ative Directory com os Serviços de Autenticação Multi-Factor AD Azure ou com serviços da Federação de Diretórios Ativos.

## <a name="secure-azure-ad-resources-using-ad-fs"></a>Proteger recursos do Azure AD com o AD FS

Para proteger o recurso da cloud, configure uma regra de afirmações para que os Serviços de Federação do Active Directory emitam a afirmação de autenticação múltipla quando um utilizador executar uma verificação de dois passos com êxito. Esta afirmação é transmitida ao Azure AD. Siga este procedimento para percorrer os passos:

1. Abra a Gestão do AD FS.
2. À esquerda, selecione **Confianças de Entidades Confiadoras**.
3. Clique com o botão direito na **Plataforma de Identidade 365 do Microsoft Office** e selecione As **Regras de Reclamação de Edição**.

   ![Consola ADFS - Confianças de Partidos Confiantes](./media/howto-mfa-adfs/trustedip1.png)

4. Em Regras de Transformação da Emissão, clique em **Adicionar Regra**.

   ![Edição Emissão Regras de Transformação](./media/howto-mfa-adfs/trustedip2.png)

5. No Assistente para Adicionar Regra de Afirmação de Transformação, selecione **Passar ou Filtrar uma Afirmação de Entrada** no menu pendente e clique em **Seguinte**.

   ![A screenshot mostra adicionar o Assistente de Regra de Reclamação de Transformação onde seleciona um modelo de regra de reclamação.](./media/howto-mfa-adfs/trustedip3.png)

6. Dê um nome à sua regra. 
7. Selecione **Referências de Métodos de Autenticação** como o tipo de Afirmação de entrada.
8. Selecione **Passar todos os valores de afirmação**.
    ![A screenshot mostra adicionar o Assistente de Regra de Reclamação de Transformação onde seleciona Passar por todos os valores de reclamação.](./media/howto-mfa-adfs/configurewizard.png)
9. Clique em **Concluir**. Feche a consola de Gestão do AD FS.

## <a name="trusted-ips-for-federated-users"></a>IPs Fidedignos para utilizadores federados

Os IPs Fidedignos permitem aos administradores ignorar a verificação em dois passos para endereços IP específicos ou para os utilizadores federados que tenham pedidos com origem na sua própria intranet. As seguintes secções descrevem como configurar a autenticação multi-factor Ad Ad com utilizadores federados e verificação em duas etapas quando um pedido é originário de uma intranet de utilizadores federados. Isto é feito ao configurar o AD FS para utilizar uma passagem ou filtrar um modelo de afirmação de entrada com o tipo de afirmação Dentro da Rede da Empresa.

Este exemplo utiliza o Microsoft 365 para os nossos Trusts de Partido Confiar.

### <a name="configure-the-ad-fs-claims-rules"></a>Configurar regras de afirmações do AD FS

A primeira coisa a fazer é configurar as afirmações do AD FS. Crie duas regras de afirmações, uma para o tipo de afirmação Dentro da Rede da Empresa e uma adicional para manter os utilizadores com sessão iniciada.

1. Abra a Gestão do AD FS.
2. À esquerda, selecione **Confianças de Entidades Confiadoras**.
3. Clique com o botão direito na **Plataforma de Identidade 365 do Microsoft Office** e selecione **Regras de Reclamação de Edição...** 
    ![ Consola ADFS - Editar regras de reivindicação](./media/howto-mfa-adfs/trustedip1.png)
4. Nas Regras de Transformação de Emissão, clique em **Adicionar Regra.** 
    ![ Adicionar uma regra de reclamação](./media/howto-mfa-adfs/trustedip2.png)
5. No Assistente para Adicionar Regra de Afirmação de Transformação, selecione **Passar ou Filtrar uma Afirmação de Entrada** no menu pendente e clique em **Seguinte**.
   ![A screenshot mostra adicionar o Assistente de Regra de Reclamação de Transformação onde seleciona Passar ou filtrar uma reclamação de entrada.](./media/howto-mfa-adfs/trustedip3.png)
6. Na caixa junto ao nome da regra de afirmação, atribua um nome à regra. Por exemplo: InsideCorpNet.
7. A partir do drop-down, ao lado do tipo de reclamação Incoming, selecione **Inside Corporate Network**.
   ![Adicionar reclamação de rede corporativa interna](./media/howto-mfa-adfs/trustedip4.png)
8. Clique em **Concluir**.
9. Em Regras de Transformação da Emissão, clique em **Adicionar Regra**.
10. No Assistente para Adicionar Regra de Afirmação de Transformação, selecione **Enviar Afirmações Utilizando uma Regra Personalizada** no menu pendente e clique em **Seguinte**.
11. Na caixa sob o nome da regra 'Reclamação': *insira manter os utilizadores assinados em*.
12. Na caixa Regra personalizada, introduza:

```ad-fs-claim-rule
        c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
            => issue(claim = c);
    ![Create custom claim to keep users signed in](./media/howto-mfa-adfs/trustedip5.png)
```

13. Clique em **Concluir**.
14. Clique em **Aplicar**.
15. Clique em **OK**.
16. Feche a Gestão do AD FS.

### <a name="configure-azure-ad-multi-factor-authentication-trusted-ips-with-federated-users"></a>Configure Azure AD Multi-Factor Authentication IPs fidedignos com utilizadores federados

Agora que as afirmações estão implementadas, podemos configurar os IPs fidedignos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Azure Ative**  >  **Directy Security**  >  **Conditional Access** Named  >  **locations**.
3. A partir do Acesso Condicional - Lâmina **de locais nomeados,** selecione **IPs fidedignos de MFA configurados**

   ![Azure AD Acesso Condicional nomeado locais Configure MFA confiáveis IPs](./media/howto-mfa-adfs/trustedip6.png)

4. Na página Definições de Serviço, em **IPs Fidedignos**, selecione **Ignorar autenticação multifator para pedidos de utilizadores federados na minha intranet**.  
5. Clique **em guardar**.

Já está! Neste momento, os utilizadores federados da Microsoft 365 só devem utilizar MFA quando uma reclamação é originária de fora da intranet corporativa.
