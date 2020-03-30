---
title: Diretrizes para azure marketplace e editor appSource [ Azure
description: Diretrizes para Azure Marketplace e AppSource para editores de aplicações e serviços
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/13/2018
ms.author: dsindona
ms.openlocfilehash: 9156126ff2a3dbe936dc426c6f5097c6bba7b7b7
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387614"
---
# <a name="guidelines"></a>Diretrizes  

<!--
## Guidelines for AppSource  
-->
---

## <a name="guidelines-for-azure-marketplace"></a>Orientações para o Mercado Azure  

### <a name="guidelines-for-creating-a-microsoft-id-to-manage-a-marketplace-account"></a>Diretrizes para criar um ID da Microsoft para gerir uma conta de mercado  
Se mais de uma pessoa necessitar de acesso ao mesmo ID da Microsoft usado para criar a sua conta de mercado, então deve seguir estas orientações para ajudá-lo a criar uma conta da empresa. 

>[!IMPORTANT]
>Para autorizar vários utilizadores a acederem à sua conta do Microsoft Developer Center (Dev Center), a Microsoft recomenda que utilize o Azure Ative Directory (Azure AD) para atribuir funções a utilizadores individuais. Cada utilizador deve aceder à conta através da assinatura com credenciais ad's individuais do Azure. Crie o seu ID microsoft utilizando um endereço de e-mail num domínio registado na sua empresa, a Microsoft sugere que o e-mail não seja atribuído a um indivíduo. Um exemplo é `windowsapps@fabrikam.com`.  
>*   Para mais informações, visite o Issue: Microsoft ID numa secção de [domínio federado azure AD.](#issue-microsoft-id-in-an-azure-ad-federated-domain)  

*   Limite o acesso ao ID da Microsoft ao menor número possível de desenvolvedores. 
*   Configurar uma lista de distribuição de e-mails corporativos (DL) que inclui todos os que devem aceder à sua conta Dev Center. Adicione o endereço de e-mail DL às suas informações de segurança. O DL permite que todos os colaboradores da lista recebam códigos de segurança quando solicitados e gerem as informações de segurança do seu ID microsoft. Se a criação de uma lista de distribuição não for viável, o proprietário da conta de e-mail individual deve estar disponível para aceder e partilhar o código de segurança quando solicitado.  
    *   Por exemplo, o proprietário é solicitado quando novas informações de segurança são adicionadas ao ID da Microsoft ou quando o ID da Microsoft é acedido a partir de um novo dispositivo.  
*   Adicione um número de telefone da empresa que não requer uma extensão e está acessível aos membros-chave da equipa.  
*   Em geral, deve exigir que os desenvolvedores utilizem dispositivos fidedignos para assinar na sua conta Dev Center. Todos os membros-chave da equipa devem ter acesso aos dispositivos fidedignos. A utilização de dispositivos fidedignos para aceder reduz a exigência de envio de códigos de segurança quando alguém acede à conta do Dev Center.  
*   Se for obrigado a conceder acesso à conta Dev Center a partir de um computador não confiável, então deve limitar o acesso a não mais de cinco desenvolvedores. Idealmente, os seus desenvolvedores devem aceder à conta a partir de computadores que partilham a mesma localização geográfica e de rede.  
*   Reveja e verifique frequentemente as suas informações de segurança.  
    *   Para ver as suas informações de segurança, visite a página de definições de Segurança localizada em [account.live.com/proofs/Manage](https://account.live.com/proofs/Manage).

A sua conta Dev Center deve ser acedida principalmente a partir de computadores fidedignos. É fundamental que aceda a partir de computadores fidedignos, porque há um limite para o número de códigos gerados por conta Dev Center por semana. A utilização de computadores fidedignos também permite a experiência de início de sessão mais segura e consistente. 
*   Para mais informações sobre as diretrizes adicionais da conta de Dev Center e segurança, visite a página de conta de desenvolvimento de Abertura localizada em [docs.microsoft.com/windows/uwp/publish/opening-a-developer-account](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account). 

---

#### <a name="issue-microsoft-id-in-an-azure-ad-federated-domain"></a>Edição: Microsoft ID num domínio federado de AD Azure  
A sua conta corporativa pode ser federada através do Azure Ative Directory (Azure AD). Se tentar criar um ID da Microsoft utilizando um endereço de e-mail corporativo federado com AD Azure, então receberá um erro. Se receber um erro, deverá consultar a sua equipa de TI para confirmar que a sua conta está federada através do Azure AD. O e-mail federado da Azure AD é um problema conhecido e a Microsoft está a trabalhar para resolvê-lo.  
*   Para mais informações sobre o Azure AD, visite a página de Documentação do Diretório Ativo Azure localizada em [docs.microsoft.com/azure/active-directory](https://docs.microsoft.com/azure/active-directory).

A Microsoft recomenda uma suver. Siga estes passos para criar `outlook.com` um novo endereço de e-mail no domínio e crie uma regra para encaminhar as suas comunicações.  
1.  Vá à página da conta Criar e clique no link 'Obter um novo link de endereço de e-mail'. 
    *   Para se inscrever no seu ID microsoft, visite a página da conta Create localizada em [signup.live.com/signup](https://signup.live.com/signup).  
2.  Crie o novo endereço de e-mail e introduza uma senha. É criado um novo ID da `outlook.com` Microsoft e uma caixa de correio de correio eletrónico no domínio. Continue o processo de registo até que a conta seja criada.  

    >[!IMPORTANT]
    >Deve utilizar um endereço de e-mail ou lista de distribuição que esteja registado como um ID da Microsoft para se registar no Dev Center. A Microsoft recomenda que utilize uma lista de distribuição para remover a dependência dos indivíduos. Se o seu endereço de e-mail ou lista de distribuição não estiver registado, então deve registar-se agora.    

    >[!Important]
    >Se o seu endereço de `Microsoft` e-mail estiver localizado no domínio da empresa, então não poderá utilizá-lo para registo no Dev Center.  

3.  Depois de criar o ID da Microsoft com o endereço de e-mail do Outlook, inscreva-se na sua caixa de correio do Outlook. Crie uma regra de reencaminhamento de e-mail. A regra de reencaminhamento de e-mail deve mover todos os e-mails recebidos na caixa de correio do Outlook para o endereço de e-mail no seu domínio que criou para gerir a sua conta de mercado.  
    *   Para iniciar sessão na sua caixa de correio do Outlook, visite a página do Outlook localizada em [outlook.live.com/owa](https://outlook.live.com/owa).  
    *   Para obter mais informações sobre o reencaminhamento de regras, visite as regras de utilização na Aplicação Web do Outlook para encaminhar automaticamente mensagens para outra página de conta localizada em [support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed](https://support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed).  

1.  A regra de reencaminhamento envia todos os e-mails e comunicações recebidos na conta de e-mail do Outlook para o endereço de e-mail num domínio registado na sua empresa. O `outlook.com` seu endereço de e-mail deve ser utilizado para autenticar tanto no Dev Center como no Cloud Partner Portal.  

## <a name="next-steps"></a>Passos seguintes

*   Visite a página do Guia da [Editora Azure Marketplace e AppSource.](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) 
 
---
