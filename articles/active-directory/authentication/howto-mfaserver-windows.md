---
title: Autenticação do Windows e o servidor de MFA do Azure - Azure Active Directory
description: A implementar a autenticação Windows e Servidor Multi-Factor Authentication do Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa52dcf08a5e4b152d9fe0db36710e41a5a79fe7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057320"
---
# <a name="windows-authentication-and-azure-multi-factor-authentication-server"></a>Autenticação do Windows e Servidor Multi-Factor Authentication do Azure

Utilize a secção Autenticação do Windows do Servidor Multi-Factor Authentication do Azure para ativar e configurar a autenticação do Windows para aplicações. Antes de configurar a Autenticação do Windows, mantenha a lista seguinte em mente:

* Após a configuração, reinicie o Multi-Factor Authentication para os Serviços de Terminal entrarem em vigor.
* Se "Exigir correspondência de utilizador do Multi-Factor Authentication do Azure" estiver selecionado e não estiver na lista de utilizadores, não conseguirá iniciar sessão no computador depois de reiniciar.
* Os IPs Fidedignos dependem do facto de a aplicação poder fornecer o IP do cliente na autenticação. Atualmente, apenas os Serviços de Terminal são suportados.  

> [!IMPORTANT]
> A partir de 1 de Julho de 2019 Microsoft já não irá oferecer servidor MFA para novas implementações. Novos clientes que gostariam de exigir a autenticação multifator de seus usuários devem utilizar com base na cloud do Azure multi-factor Authentication. Os clientes existentes que tenham ativado o servidor de MFA antes de 1 de Julho poderão transferir a versão mais recente, as atualizações futuras e gerar credenciais de ativação como de costume.

> [!NOTE]
> Esta funcionalidade não é suportada para proteger os Serviços de Terminal no Windows Server 2012 R2.

## <a name="to-secure-an-application-with-windows-authentication-use-the-following-procedure"></a>Para proteger uma aplicação com a autenticação do Windows, utilize o procedimento seguinte

1. No Servidor Multi-Factor Authentication do Azure, clique no ícone Autenticação do Windows.
   ![Autenticação do Windows no servidor MFA](./media/howto-mfaserver-windows/windowsauth.png)
2. Selecione a caixa de verificação **Ativar Autenticação do Windows**. Por predefinição, esta caixa está desmarcada.
3. O separador Aplicações permite ao administrador configurar uma ou mais aplicações para a Autenticação do Windows.
4. Selecione um servidor ou uma aplicação e especifique se o servidor/aplicação está ativado. Clique em **OK**.
5. Clique em **Adicionar...**
6. O separador IPs Fidedignos permite ignorar sessões o Multi-Factor Authentication do Azure para as sessões do Windows com origem em IPs específicos. Por exemplo, se os empregados utilizarem a aplicação a partir do escritório e de casa, pode decidir que não pretende que os respetivos telefones toquem para o Multi-Factor Authentication do Azure enquanto estiverem no escritório. Para tal, especifique a sub-rede do escritório como uma entrada de IPs Fidedignos.
7. Clique em **Adicionar...**
8. Selecione **IP Único** se gostaria de ignorar um único endereço IP.
9. Selecione **Intervalo de IP** se gostaria de ignorar um intervalo de IP completo. Por exemplo, 10.63.193.1-10.63.193.100.
10. Selecione **Sub-rede** se pretende especificar um intervalo de IPs utilizando a notação de sub-rede. Introduza o IP inicial da sub-rede e escolha a máscara de rede adequada na lista pendente.
11. Clique em **OK**.

## <a name="next-steps"></a>Passos Seguintes

- [Configurar aplicações de VPN de terceiros para o Servidor de MFA do Azure](howto-mfaserver-nps-vpn.md)

- [Aumente a infraestrutura de autenticação existente com a extensão NPS para a MFA do Azure](howto-mfa-nps-extension.md)
