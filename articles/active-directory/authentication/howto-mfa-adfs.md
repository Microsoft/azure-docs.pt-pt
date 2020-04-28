---
title: Recursos seguros com O MFA azure e ADFS - Diretório Ativo Azure
description: Esta é a página do Multi-Factor Authentication do Azure que descreve como começar a utilizar o MFA do Azure e o AD FS na nuvem.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5949fdce9d591a9c02c5c6c4fb2f1dde94c45d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80654023"
---
# <a name="securing-cloud-resources-with-azure-multi-factor-authentication-and-ad-fs"></a>Proteger recursos da nuvem com o Multi-Factor Authentication do Azure e o AD FS

Se a sua organização estiver federada no Azure Active Directory, utilize o Multi-Factor Authentication do Azure ou os Serviços de Federação do Active Directory (AD FS) para proteger os recursos acedidos pelo Azure AD. Utilize os procedimentos seguintes para proteger os recursos do Azure Active Directory com o Multi-Factor Authentication do Azure ou os Serviços de Federação do Active Directory (AD FS).

## <a name="secure-azure-ad-resources-using-ad-fs"></a>Proteger recursos do Azure AD com o AD FS

Para proteger o recurso da cloud, configure uma regra de afirmações para que os Serviços de Federação do Active Directory emitam a afirmação de autenticação múltipla quando um utilizador executar uma verificação de dois passos com êxito. Esta afirmação é transmitida ao Azure AD. Siga este procedimento para percorrer os passos:

1. Abra a Gestão do AD FS.
2. À esquerda, selecione **Confianças de Entidades Confiadoras**.
3. Clique à direita na **Plataforma de Identidade do Microsoft Office 365** e selecione Regras de **Reclamação de Edição**.

   ![Consola ADFS - Confiança de PartidoS](./media/howto-mfa-adfs/trustedip1.png)

4. Em Regras de Transformação da Emissão, clique em **Adicionar Regra**.

   ![Emissão de emissão transforma regras](./media/howto-mfa-adfs/trustedip2.png)

5. No Assistente para Adicionar Regra de Afirmação de Transformação, selecione **Passar ou Filtrar uma Afirmação de Entrada** no menu pendente e clique em **Seguinte**.

   ![Assistente para Adicionar Regra de Afirmação de Transformação](./media/howto-mfa-adfs/trustedip3.png)

6. Dê um nome à sua regra. 
7. Selecione **Referências de Métodos de Autenticação** como o tipo de Afirmação de entrada.
8. Selecione **Passar todos os valores de afirmação**.
    ![Assistente para Adicionar Regra de Afirmação de Transformação](./media/howto-mfa-adfs/configurewizard.png)
9. Clique em **Concluir**. Feche a consola de Gestão do AD FS.

## <a name="trusted-ips-for-federated-users"></a>IPs Fidedignos para utilizadores federados

Os IPs Fidedignos permitem aos administradores ignorar a verificação em dois passos para endereços IP específicos ou para os utilizadores federados que tenham pedidos com origem na sua própria intranet. As secções seguintes descrevem como configurar IPs Fidedignos do Multi-Factor Authentication do Azure com utilizadores federados e ignorar a verificação em dois passos quando um pedido tem origem na intranet dos utilizadores federados. Isto é feito ao configurar o AD FS para utilizar uma passagem ou filtrar um modelo de afirmação de entrada com o tipo de afirmação Dentro da Rede da Empresa.

Este exemplo utiliza o Office 365 para as Confianças de Entidades Confiadoras.

### <a name="configure-the-ad-fs-claims-rules"></a>Configurar regras de afirmações do AD FS

A primeira coisa a fazer é configurar as afirmações do AD FS. Crie duas regras de afirmações, uma para o tipo de afirmação Dentro da Rede da Empresa e uma adicional para manter os utilizadores com sessão iniciada.

1. Abra a Gestão do AD FS.
2. À esquerda, selecione **Confianças de Entidades Confiadoras**.
3. Clique à direita na **Plataforma de Identidade do Microsoft Office 365** e selecione Regras de Reclamação de **Edição...** 
   Consola ADFS - Editar Regras de ![Reclamação](./media/howto-mfa-adfs/trustedip1.png)
4. Em Regras de Transformação de Emissão, clique em **Adicionar Regra.** 
   Adicionar uma regra de ![reclamação](./media/howto-mfa-adfs/trustedip2.png)
5. No Assistente para Adicionar Regra de Afirmação de Transformação, selecione **Passar ou Filtrar uma Afirmação de Entrada** no menu pendente e clique em **Seguinte**.
   ![Assistente para Adicionar Regra de Afirmação de Transformação](./media/howto-mfa-adfs/trustedip3.png)
6. Na caixa junto ao nome da regra de afirmação, atribua um nome à regra. Por exemplo: InsideCorpNet.
7. A partir da queda, ao lado do tipo de reclamação de entrada, selecione **Inside Corporate Network**.
   ![Adicionar alegação de Rede Corporativa Interna](./media/howto-mfa-adfs/trustedip4.png)
8. Clique em **Concluir**.
9. Em Regras de Transformação da Emissão, clique em **Adicionar Regra**.
10. No Assistente para Adicionar Regra de Afirmação de Transformação, selecione **Enviar Afirmações Utilizando uma Regra Personalizada** no menu pendente e clique em **Seguinte**.
11. Na caixa sob o nome da regra 'Reclamação': insira *Os Utilizadores Inscritos em*.
12. Na caixa Regra personalizada, introduza:

        c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
            => issue(claim = c);
    ![Criar reivindicação personalizada para manter os utilizadores inscritos](./media/howto-mfa-adfs/trustedip5.png)
13. Clique em **Concluir**.
14. Clique em **Aplicar**.
15. Clique em **OK**.
16. Feche a Gestão do AD FS.

### <a name="configure-azure-multi-factor-authentication-trusted-ips-with-federated-users"></a>Configurar IPs Fidedignos do Multi-Factor Authentication do Azure com Utilizadores Federados

Agora que as afirmações estão implementadas, podemos configurar os IPs fidedignos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione Acesso**Condicional** > de**Segurança** >  **ativa** > azure**Localização nomeada**.
3. A partir do Acesso Condicional - Lâmina de **localização nomeada,** selecione **Configure MFA fidedigno de IPs**

   ![Acesso Condicional Azure AD nomeado locais Configure MFA fidedignos IPs](./media/howto-mfa-adfs/trustedip6.png)

4. Na página Definições de Serviço, em **IPs Fidedignos**, selecione **Ignorar autenticação multifator para pedidos de utilizadores federados na minha intranet**.  
5. Clique em **guardar**.

Já está! Neste momento, os utilizadores federados do Office 365 apenas têm de utilizar o MFA quando uma afirmação tiver origem fora da intranet da empresa.
