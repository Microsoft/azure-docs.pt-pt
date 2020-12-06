---
title: Upgrade PhoneFactor para Azure MFA Server - Azure Ative Directory
description: Começar a utilizar o Servidor de MFA do Azure e quando atualizar a partir do phonefactor agent mais antigo.
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
ms.openlocfilehash: 808109e00decf5c4084be5be550b49e2e7d4628c
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96742362"
---
# <a name="upgrade-the-phonefactor-agent-to-azure-multi-factor-authentication-server"></a>Atualizar o PhoneFactor Agent para o Servidor Multi-Factor Authentication do Azure

Para atualizar o PhoneFactor Agent v5.x ou o mais antigo para o Servidor Multi-Factor Authentication do Azure, desinstale o PhoneFactor Agent e os componentes afiliados primeiro. Em seguida, o Servidor Multi-Factor Authentication e os respetivos componentes afiliados podem ser instalados.

> [!IMPORTANT]
> A partir de 1 de julho de 2019, a Microsoft já não oferece O MFA Server para novas implementações. Os novos clientes que pretendam exigir a autenticação de vários fatores (MFA) durante os eventos de entrada devem utilizar a autenticação multi-factor Azure AD baseada na nuvem.
>
> Para começar com mFA baseado na nuvem, consulte [Tutorial: Secure user in events with Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Os clientes existentes que ativaram o MFA Server antes de 1 de julho de 2019 podem descarregar a versão mais recente, futuras atualizações e gerar credenciais de ativação como de costume.

## <a name="uninstall-the-phonefactor-agent"></a>Desinstalar o PhoneFactor Agent

1. Primeiro, faça uma cópia de segurança do ficheiro de dados do PhoneFactor. A localização de instalação predefinida é C:\Programas\PhoneFactor\Data\Phonefactor.pfdata.

2. Se o Portal de Utilizador estiver instalado:
   1. Navegue para a pasta de instalação e faça uma cópia de segurança do ficheiro web.config. A localização de instalação predefinida é C:\inetpub\wwwroot\PhoneFactor.

   2. Se tiver adicionado temas personalizados ao portal, faça uma cópia de segurança da pasta personalizada abaixo do diretório C:\inetpub\wwwroot\PhoneFactor\App_Themes.

   3. Desinstale o Portal de Utilizador através do PhoneFactor Agent (disponível apenas se estiver instalado no mesmo servidor do PhoneFactor Agent) ou através dos Programas e Funcionalidades do Windows.

3. Se o Serviço Web da Aplicação Móvel estiver instalado:

   1. Vá para a pasta de instalação e faça uma cópia de segurança do ficheiro web.config. A localização de instalação predefinida é C:\inetpub\wwwroot\PhoneFactorPhoneAppWebService.

   2. Desinstale o Serviço Web da Aplicação Móvel através dos Programas e Funcionalidades do Windows.

4. Se o SDK do Serviço Web estiver instalado, desinstale-o através do PhoneFactor Agent ou dos Programas e Funcionalidades do Windows.

5. Desinstale o PhoneFactor Agent através dos Programas e Funcionalidades do Windows.

## <a name="install-the-multi-factor-authentication-server"></a>Instale o Servidor Multi-Factor Authentication

O caminho de instalação é escolhido a partir do registo da instalação do PhoneFactor Agent anterior, pelo que deve instalar na mesma localização (por exemplo, C:\Programas\PhoneFactor). As novas instalações têm um caminho de instalação predefinido diferente (por exemplo, C:\Programas\Multi-Factor Authentication Server). O ficheiro de dados deixado pelo PhoneFactor Agent anterior deve ser atualizado durante a instalação, pelo que os seus utilizadores e definições devem permanecer depois de instalar o novo Servidor Multi-Factor Authentication.

1. Se lhe for solicitado, ative o Servidor Multi-Factor Authentication e certifique-se de que é atribuído ao grupo de replicação correto.

2. Se o SDK do Serviço Web tiver sido instalado anteriormente, instale o novo SDK do Serviço Web através da Interface de Utilizador do Servidor Multi-Factor Authentication.

   O nome de diretório virtual padrão é agora **MultiFactorAuthWebServiceSdk** em vez de **PhoneFactorWebServiceSdk**. Se pretender utilizar o nome anterior, tem de alterar o nome do diretório virtual durante a instalação. Caso contrário, se permitir que a instalação utilize o novo nome predefinido, tem de alterar o URL em todas as aplicações que referenciam o SDK do Serviço Web, (como o Portal de Utilizador e o Serviço Web da Aplicação Móvel), para apontarem para a localização correta.

3. Se o Portal de Utilizador tiver sido instalado anteriormente no Servidor do PhoneFactor Agent, instale o novo Portal de Utilizador do Multi-Factor Authentication através da Interface de Utilizador do Servidor Multi-Factor Authentication.

   O nome de diretório virtual padrão é agora **MultiFactorAuth** em vez de **PhoneFactor**. Se pretender utilizar o nome anterior, tem de alterar o nome do diretório virtual durante a instalação. Caso contrário, se permitir que a instalação utilize o novo nome predefinido, deve clicar no ícone do Portal de Utilizador no Servidor Multi-Factor Authentication e atualizar o URL do Portal de Utilizador no separador Definições.

4. Se o Portal de Utilizador e/ou o Serviço Web da Aplicação Móvel tiverem sido instalados anteriormente num servidor diferente do PhoneFactor Agent:

   1. Vá para a localização de instalação (por exemplo, C:\Programas\PhoneFactor) e copie um ou mais instaladores adequados para o outro servidor. Existem instaladores de 32 e 64 bits para o Portal de Utilizador e o Serviço Web da Aplicação Móvel. São denominados MultiFactorAuthenticationUserPortalSetupXX.msi e MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi.

   2. Para instalar o Portal de Utilizador no servidor Web, abra uma linha de comandos como administrador e execute MultiFactorAuthenticationUserPortalSetupXX.msi.

      O nome de diretório virtual padrão é agora **MultiFactorAuth** em vez de **PhoneFactor**. Se pretender utilizar o nome anterior, tem de alterar o nome do diretório virtual durante a instalação. Caso contrário, se permitir a instalação utilizar o novo nome predefinido, deverá clicar no ícone Do Portal do Utilizador no Servidor de Autenticação Multi-Factor e atualizar o URL do Portal do Utilizador no separador Definições. Os utilizadores existentes precisam de ser informados do novo URL.

   3. Vá para a localização de instalação do Portal de Utilizador (por exemplo, C:\inetpub\wwwroot\MultiFactorAuth) e edite o ficheiro web.config. Copie os valores nas secções appSettings e applicationSettings a partir do ficheiro web.config original cuja cópia de segurança foi feita antes da atualização para o novo ficheiro web.config. Se o novo nome do diretório virtual predefinido foi mantido quando instalou o SDK do Serviço Web, altere o URL na secção applicationSettings para apontar para a localização correta. Se quaisquer outras predefinições foram alteradas no ficheiro web.config anterior, aplique as mesmas alterações ao novo ficheiro web.config.

> [!NOTE]
> Ao atualizar de uma versão do Servidor MFA do Azure anterior à 8.0 até à 8.0+ que o serviço Web da aplicação móvel pode ser desinstalado após a atualização

## <a name="next-steps"></a>Passos seguintes

- [Instale o portal de utilizadores](howto-mfaserver-deploy-userportal.md) do Servidor Multi-Factor Authentication do Azure.

- [Configurar a Autenticação do Windows](howto-mfaserver-windows.md) nas suas aplicações. 
