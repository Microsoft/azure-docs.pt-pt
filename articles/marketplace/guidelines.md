---
title: Diretrizes para o publicador do Azure Marketplace e AppSource | Azure
description: Diretrizes para o Azure Marketplace e AppSource para editores de aplicações e serviços
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/13/2018
ms.author: ellacroi
ms.openlocfilehash: a0ab88b1375f5178ca6f41689de8d2f30f8d7808
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728136"
---
# <a name="guidelines"></a>Diretrizes  

<!--
## Guidelines for AppSource  
-->
---

## <a name="guidelines-for-azure-marketplace"></a>Diretrizes para o Azure Marketplace  

### <a name="guidelines-for-creating-a-microsoft-id-to-manage-a-marketplace-account"></a>Diretrizes para criar um ID da Microsoft para gerir uma conta do marketplace  
Se mais de uma pessoa precisa de acesso para o mesmo ID Microsoft utilizada para criar a sua conta do marketplace, em seguida, deve seguir estas diretrizes para ajudar a criar uma conta da empresa. 

>[!IMPORTANT]
>Para autorizar a vários utilizadores para aceder à sua conta do Microsoft Developer Center (Centro de desenvolvimento), a Microsoft recomenda que utilize o Azure Active Directory (Azure AD) para atribuir funções a utilizadores individuais. Cada utilizador tem de aceder à conta ao iniciar sessão com a pessoa credenciais do Azure AD. Criar o seu ID da Microsoft utilizando um endereço de e-mail num domínio registado para a sua empresa Microsoft sugere que a mensagem de e-mail não ser atribuídos a um indivíduo. Um exemplo é `windowsapps@fabrikam.com`.  
>*   Para obter mais informações, visite o [problema: ID da Microsoft num Azure AD federado domínio](#issue-microsoft-id-in-an-azure-ad-federated-domain) secção.  

*   Limitar o acesso para o ID da Microsoft para o menor número possível de desenvolvedores. 
*   Configure uma lista de distribuição (DL) de e-mail da empresa que inclui todas as pessoas que têm de aceder a sua conta do Dev Center. Adicione o endereço de e-mail DL às suas informações de segurança. A DL permite que todos os funcionários na lista para receber códigos de segurança quando solicitado e para gerenciar as informações de segurança de sua ID. Microsoft Se não é possível configurar uma lista de distribuição, em seguida, o proprietário da conta de individual email tem de estar disponível para acessar e compartilhar o código de segurança quando lhe for pedido.  
    *   Por exemplo, o proprietário é solicitado quando novas informações de segurança são adicionadas para o ID da Microsoft ou quando o ID da Microsoft é acessado a partir de um novo dispositivo.  
*   Adicione um número de telefone da empresa que não necessita de uma extensão e está acessível para os membros da equipe de chave.  
*   Em geral, deve exigir que os desenvolvedores usem dispositivos fidedignos para iniciar sessão na sua conta do Dev Center. Todos os membros da equipe de chave devem ter acesso aos dispositivos fidedignos. Utilizar dispositivos fidedignos para aceder ao reduz o requisito para o envio de códigos de segurança quando alguém está a aceder a conta do Dev Center.  
*   Se tem de conceder acesso à conta do Dev Center a partir de um computador não fidedigno, em seguida, deve limitar o acesso a mais do que cinco desenvolvedores. O ideal é que os programadores devem aceder à conta de computadores que partilham o mesmo geográfica e a localização de rede.  
*   Com frequência, rever e confirmar suas informações de segurança.  
    *   Para ver as suas informações de segurança, visite a página de definições localizada em segurança [account.live.com/proofs/Manage](https://account.live.com/proofs/Manage).

Sua conta do Dev Center deve ser acessada principalmente de computadores fidedignos. É fundamental que acessar a partir de computadores fidedignos, porque existe um limite ao número de códigos gerados por conta do Dev Center por semana. Usando a computadores fidedignos também permite que a experiência início de sessão mais segura e consistente. 
*   Para obter mais informações sobre as diretrizes de conta do Dev Center e segurança adicionais, visite a abertura de uma página de conta de desenvolvedor localizada em [docs.microsoft.com/windows/uwp/publish/opening-a-developer-account](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account). 

---

#### <a name="issue-microsoft-id-in-an-azure-ad-federated-domain"></a>Problema: ID da Microsoft num domínio federado do Azure AD  
Sua conta empresarial pode ser federada no Azure Active Directory (Azure AD). Se tentar criar um ID de Microsoft utilizando um endereço de e-mail da empresa que está Federado com o Azure AD, em seguida, receberá um erro. Se receber um erro, deve verificar com a sua equipa de TI para confirmar a que sua conta está federada através do Azure AD. E-mail do AD em federado do Azure é um problema conhecido e a Microsoft está trabalhando para resolvê-lo.  
*   Para obter mais informações sobre o Azure AD, visite a documentação do Azure Active Directory página localizada em [docs.microsoft.com/azure/active-directory](https://docs.microsoft.com/azure/active-directory).

A Microsoft recomenda uma solução alternativa. Siga estes passos para criar um novo endereço de e-mail no `outlook.com` domínio e criar uma regra para encaminhar as comunicações.  
1.  Vá para a página de criação de conta e clicar em Get numa nova ligação de endereço de e-mail. 
    *   Para se inscrever para o seu ID da Microsoft, visite a página de conta criar localizada em [signup.live.com/signup](https://signup.live.com/signup).  
2.  Criar o novo endereço de e-mail e introduza uma palavra-passe. Um novo ID de Microsoft e uma caixa de correio de correio eletrónico no `outlook.com` domínio é criado. Continue o processo de registo até que a conta é criada.  

    >[!IMPORTANT]
    >Tem de utilizar uma lista de endereços ou a distribuição de e-mail está registrada como um ID da Microsoft para se registar no Dev Center. A Microsoft recomenda que utilize uma lista de distribuição para remover a dependência das pessoas. Se não estiver registrada a sua lista de endereços ou a distribuição de e-mail, em seguida, deve registrar agora.    

    >[!Important]
    >Se a qualquer endereço de e-mail está localizado no `Microsoft` da empresa domínio, em seguida, não é possível usá-lo para o registo no Dev Center.  

3.  Depois de criar o ID da Microsoft com o endereço de e-mail do Outlook, inicie sessão na sua caixa de correio do Outlook. Crie uma regra de encaminhamento de mensagem de e-mail. A regra de encaminhamento de mensagem de e-mail deve mover todos os e-mails que são recebidos na caixa de correio da Outlook para o endereço de e-mail no seu domínio que criou para gerir a sua conta do marketplace.  
    *   Para iniciar sessão na sua caixa de correio do Outlook, visite a página de Outlook localizada em [outlook.live.com/owa](https://outlook.live.com/owa).  
    *   Para obter mais informações sobre as regras de reencaminhamento, visite as regras de utilização no Outlook Web App automaticamente encaminhar mensagens para outra página de conta localizado em [support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed](https://support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed).  

1.  A regra de encaminhamento envia todas as comunicações recebidas na conta de e-mail do Outlook para o endereço de e-mail num domínio registado para a sua empresa e e-mail. Sua `outlook.com` endereço de e-mail tem de ser utilizado para autenticar no Centro de desenvolvimento e no Portal de parceiros da Cloud.  

## <a name="next-steps"></a>Passos Seguintes
*   Visite o [Azure Marketplace e guia de publicador do AppSource](./marketplace-publishers-guide.md) página.  
 
---
