---
title: Diretrizes para o Azure Marketplace e o AppSource Publisher | Azure
description: Diretrizes para o Azure Marketplace e o AppSource para Publicadores de aplicativo e serviço
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: jm-aditi-ms
manager: pabutler
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 06/13/2018
ms.author: ellacroi
ms.openlocfilehash: 371f36e13c244439a583cbeb7ff06c6a1283d272
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825942"
---
# <a name="guidelines"></a>Diretrizes  

<!--
## Guidelines for AppSource  
-->
---

## <a name="guidelines-for-azure-marketplace"></a>Diretrizes para o Azure Marketplace  

### <a name="guidelines-for-creating-a-microsoft-id-to-manage-a-marketplace-account"></a>Diretrizes para criar uma ID da Microsoft para gerenciar uma conta do Marketplace  
Se mais de uma pessoa exigir acesso à mesma ID da Microsoft usada para criar sua conta do Marketplace, você deverá seguir estas diretrizes para ajudá-lo a criar uma conta da empresa. 

>[!IMPORTANT]
>Para autorizar vários usuários a acessar sua conta do centro de desenvolvedores da Microsoft (dev Center), a Microsoft recomenda que você use Azure Active Directory (AD do Azure) para atribuir funções a usuários individuais. Cada usuário deve acessar a conta entrando com as credenciais individuais do Azure AD. Crie sua ID da Microsoft usando um endereço de email em um domínio registrado para sua empresa a Microsoft sugere que o email não seja atribuído a um indivíduo. Um exemplo é `windowsapps@fabrikam.com`.  
>*   Para obter mais informações, visite a seção [problema: ID da Microsoft em um domínio federado do Azure ad](#issue-microsoft-id-in-an-azure-ad-federated-domain) .  

*   Limite o acesso à ID da Microsoft para o menor número possível de desenvolvedores. 
*   Configure uma DL (lista de distribuição de email) corporativa que inclui todos que devem acessar sua conta do centro de desenvolvimento. Adicione o endereço de email do DL às suas informações de segurança. A DL permite que todos os funcionários da lista recebam códigos de segurança quando solicitados e gerenciem as informações de segurança para sua ID da Microsoft. Se a configuração de uma lista de distribuição não for viável, o proprietário da conta de email individual deverá estar disponível para acessar e compartilhar o código de segurança quando solicitado.  
    *   Por exemplo, o proprietário é solicitado quando novas informações de segurança são adicionadas à ID da Microsoft ou quando a ID da Microsoft é acessada de um novo dispositivo.  
*   Adicione um número de telefone da empresa que não exija uma extensão e que esteja acessível aos principais membros da equipe.  
*   Em geral, você deve exigir que os desenvolvedores usem dispositivos confiáveis para entrar em sua conta do centro de desenvolvimento. Todos os principais membros da equipe devem ter acesso aos dispositivos confiáveis. Usar dispositivos confiáveis para acessar reduz a necessidade de enviar códigos de segurança quando alguém está acessando a conta do centro de desenvolvimento.  
*   Se for necessário conceder acesso à conta do centro de desenvolvimento de um computador não confiável, você deverá limitar o acesso a não mais do que cinco desenvolvedores. O ideal é que os desenvolvedores acessem a conta de computadores que compartilham o mesmo local geográfico e de rede.  
*   Revise com frequência e verifique suas informações de segurança.  
    *   Para exibir suas informações de segurança, visite a página Configurações de segurança localizada em [Account.Live.com/Proofs/Manage](https://account.live.com/proofs/Manage).

Sua conta do centro de desenvolvimento deve ser acessada principalmente de computadores confiáveis. É fundamental que você acesse os computadores confiáveis, pois há um limite para o número de códigos gerados por conta do centro de desenvolvimento por semana. O uso de computadores confiáveis também permite a experiência de entrada mais segura e consistente. 
*   Para obter mais informações sobre diretrizes e segurança adicionais da conta do centro de desenvolvimento, visite a página abrindo uma conta de desenvolvedor localizada em [docs.Microsoft.com/Windows/UWP/Publish/Opening-a-Developer-Account](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account). 

---

#### <a name="issue-microsoft-id-in-an-azure-ad-federated-domain"></a>Problema: ID da Microsoft em um domínio federado do Azure AD  
Sua conta corporativa pode ser federada por meio do Azure Active Directory (AD do Azure). Se você tentar criar uma ID da Microsoft usando um endereço de email corporativo federado com o Azure AD, você receberá um erro. Se você receber um erro, deverá verificar com sua equipe de ti para confirmar se sua conta é federada por meio do Azure AD. O email federado do Azure AD é um problema conhecido e a Microsoft está trabalhando para solucioná-lo.  
*   Para obter mais informações sobre o Azure AD, visite a página de documentação Azure Active Directory localizada em [docs.Microsoft.com/Azure/Active-Directory](https://docs.microsoft.com/azure/active-directory).

A Microsoft recomenda uma solução alternativa. Siga estas etapas para criar um novo endereço de email no domínio `outlook.com` e criar uma regra para encaminhar suas comunicações.  
1.  Vá para a página Criar conta e clique no link obter um novo endereço de email. 
    *   Para se inscrever em sua ID da Microsoft, visite a página Criar conta localizada em [signup.Live.com/signup](https://signup.live.com/signup).  
2.  Crie o novo endereço de email e insira uma senha. Uma nova ID da Microsoft e uma caixa de correio de email no domínio de `outlook.com` são criadas. Continue o processo de registro até que a conta seja criada.  

    >[!IMPORTANT]
    >Você deve usar um endereço de email ou uma lista de distribuição registrada como uma ID da Microsoft para se registrar no centro de desenvolvimento. A Microsoft recomenda que você use uma lista de distribuição para remover a dependência de indivíduos. Se seu endereço de email ou lista de distribuição não estiver registrado, você deverá registrar agora.    

    >[!Important]
    >Se seu endereço de email estiver localizado no domínio da empresa `Microsoft`, você não poderá usá-lo para registro no centro de desenvolvimento.  

3.  Depois de criar a ID da Microsoft com o endereço de email do Outlook, entre na caixa de correio do Outlook. Criar uma regra de encaminhamento de email. A regra de encaminhamento de email deve mover todos os emails recebidos na caixa de correio do Outlook para o endereço de email no seu domínio que você criou para gerenciar sua conta do Marketplace.  
    *   Para entrar na caixa de correio do Outlook, visite a página do Outlook localizada em [Outlook.Live.com/owa](https://outlook.live.com/owa).  
    *   Para obter mais informações sobre regras de encaminhamento, visite a página usar regras no Outlook Web App para encaminhar mensagens automaticamente para outra conta localizada em [support.Office.com/article/use-Rules-in-Outlook-Web-App-to-automatically-Forward-messages-to-Another-Account-1433e3a0-7fb0-4999-b536-50e05cb67fed](https://support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed).  

1.  A regra de encaminhamento envia todo o email e as comunicações recebidas na conta de email do Outlook para o endereço de email em um domínio registrado para sua empresa. Seu endereço de email `outlook.com` deve ser usado para autenticar no centro de desenvolvimento e Portal do Cloud Partner.  

## <a name="next-steps"></a>Passos seguintes

*   Visite a página do [Guia do Azure Marketplace e do AppSource Publisher](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) . 
 
---
