---
title: Autenticação do Windows e Servidor Azure MFA - Diretório Ativo Azure
description: A implementar a autenticação Windows e Servidor Multi-Factor Authentication do Azure
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
ms.openlocfilehash: 962f022e5ed38e5c60f327fa4f85a5edc872b938
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96742053"
---
# <a name="windows-authentication-and-azure-multi-factor-authentication-server"></a>Autenticação do Windows e Servidor Multi-Factor Authentication do Azure

Utilize a secção Autenticação do Windows do Servidor Multi-Factor Authentication do Azure para ativar e configurar a autenticação do Windows para aplicações. Antes de configurar a Autenticação do Windows, mantenha a lista seguinte em mente:

* Após a configuração, reinicie o Multi-Factor Authentication para os Serviços de Terminal entrarem em vigor.
* Se 'Exigir a autenticação multi-factor'' for verificada e não estiver na lista de utilizadores, não poderá iniciar sessão na máquina após o reboot.
* Os IPs Fidedignos dependem do facto de a aplicação poder fornecer o IP do cliente na autenticação. Atualmente, apenas os Serviços de Terminal são suportados.  

> [!IMPORTANT]
> A partir de 1 de julho de 2019, a Microsoft já não oferece O MFA Server para novas implementações. Os novos clientes que pretendam exigir a autenticação de vários fatores (MFA) durante os eventos de entrada devem utilizar a autenticação multi-factor Azure AD baseada na nuvem.
>
> Para começar com mFA baseado na nuvem, consulte [Tutorial: Secure user in events with Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Os clientes existentes que ativaram o MFA Server antes de 1 de julho de 2019 podem descarregar a versão mais recente, futuras atualizações e gerar credenciais de ativação como de costume.

> [!NOTE]
> Esta funcionalidade não é suportada para proteger os Serviços de Terminal no Windows Server 2012 R2.

## <a name="to-secure-an-application-with-windows-authentication-use-the-following-procedure"></a>Para garantir uma aplicação com autenticação do Windows, utilize o seguinte procedimento

1. No Servidor Multi-Factor Authentication do Azure, clique no ícone Autenticação do Windows.
   ![Autenticação do Windows no Servidor MFA](./media/howto-mfaserver-windows/windowsauth.png)
2. Selecione a caixa de verificação **Ativar Autenticação do Windows**. Por predefinição, esta caixa está desmarcada.
3. O separador Aplicações permite ao administrador configurar uma ou mais aplicações para a Autenticação do Windows.
4. Selecione um servidor ou uma aplicação e especifique se o servidor/aplicação está ativado. Clique em **OK**.
5. Clique **em Adicionar...**
6. O separador IPs Fidedignos permite ignorar sessões o Multi-Factor Authentication do Azure para as sessões do Windows com origem em IPs específicos. Por exemplo, se os empregados utilizarem a aplicação a partir do escritório e de casa, pode decidir que não pretende que os respetivos telefones toquem para o Multi-Factor Authentication do Azure enquanto estiverem no escritório. Para tal, especifique a sub-rede do escritório como uma entrada de IPs Fidedignos.
7. Clique **em Adicionar...**
8. Selecione **O IP único** se quiser saltar um único endereço IP.
9. Selecione **Intervalo de IP** se gostaria de ignorar um intervalo de IP completo. Por exemplo, 10.63.193.1-10.63.193.100.
10. Selecione **Sub-rede** se pretende especificar um intervalo de IPs utilizando a notação de sub-rede. Introduza o IP inicial da sub-rede e escolha a máscara de rede adequada na lista pendente.
11. Clique em **OK**.

## <a name="next-steps"></a>Passos seguintes

- [Configurar aplicações de VPN de terceiros para o Servidor de MFA do Azure](howto-mfaserver-nps-vpn.md)

- [Aumente a infraestrutura de autenticação existente com a extensão NPS para a MFA do Azure](howto-mfa-nps-extension.md)
