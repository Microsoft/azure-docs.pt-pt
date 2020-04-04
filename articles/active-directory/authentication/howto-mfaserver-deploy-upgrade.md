---
title: Upgrade Azure MFA Server - Diretório Ativo Azure
description: Passos e orientação para atualizar o Servidor de Autenticação Multi-Factor Azure para uma versão mais recente.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/12/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f242b4a7e984ceeb183547cb3a949927f3c91da
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653106"
---
# <a name="upgrade-to-the-latest-azure-multi-factor-authentication-server"></a>Atualização para o Servidor Multi-Factor Authentication do Azure mais recente

Este artigo acompanha-o através do processo de atualização do Servidor de Autenticação Multi-Factor Azure (MFA) v6.0 ou superior. Se necessitar de atualizar uma versão antiga do Agente PhoneFactor, consulte o Upgrade do Agente PhoneFactor para o Servidor de [Autenticação Multifactor Azure](howto-mfaserver-deploy-upgrade-pf.md).

Se estiver a atualizar de v6.x ou mais velho para v7.x ou mais recente, todos os componentes mudam de .NET 2.0 para .NET 4.5. Todos os componentes também requerem microsoft Visual C++ 2015 Redistribuable Update 1 ou superior. O instalador MFA Server instala as versões x86 e x64 destes componentes se ainda não estiverem instalados. Se o Portal do Utilizador e o Serviço Web da Aplicação Móvel funcionarem em servidores separados, é necessário instalar esses pacotes antes de atualizar esses componentes. Pode pesquisar a mais recente atualização redistribuível do Microsoft Visual C++ 2015 no [Microsoft Download Center](https://www.microsoft.com/download/). 

> [!IMPORTANT]
> A partir de 1 de julho de 2019, a Microsoft deixará de oferecer o MFA Server para novas implementações. Os novos clientes que pretendam exigir a autenticação de vários fatores dos seus utilizadores devem utilizar a autenticação multi-factor Azure baseada na nuvem. Os clientes existentes que ativaram o MFA Server antes do dia 1 de julho poderão descarregar a versão mais recente, futuras atualizações e gerar credenciais de ativação como de costume.

Etapas de upgrade num ápice:

* Upgrade Azure MFA Servers (Subordinados e ntão Mestre)
* Atualizar as instâncias do Portal do Utilizador
* Atualizar as instâncias adaptadores AD FS

## <a name="upgrade-azure-mfa-server"></a>Upgrade Azure MFA Server

1. Utilize as instruções no Download do Servidor de [Autenticação Multi-Factor Azure](howto-mfaserver-deploy.md#download-the-mfa-server) para obter a versão mais recente do instalador do Servidor Azure MFA.
2. Faça uma cópia de segurança do ficheiro de dados do Servidor MFA localizado no C:\Program Files\Multi-Factor Authentication Server\Data\PhoneFactor.pfdata (assumindo a localização de instalação padrão) no seu servidor MFA principal.
3. Se executar vários servidores para uma elevada disponibilidade, altere os sistemas de clientes que autenticam o Servidor MFA para que deixem de enviar tráfego para os servidores que estão a atualizar. Se utilizar um balanceador de carga, remova um Servidor MFA subordinado do equilibrador de carga, faça a atualização e, em seguida, adicione o servidor de volta à exploração.
4. Execute o novo instalador em cada Servidor MFA. Atualize os servidores subordinados primeiro porque podem ler o antigo ficheiro de dados que está a ser replicado pelo mestre.

   > [!NOTE]
   > Ao atualizar um servidor, deve ser removido de qualquer equilíbrio de carga ou partilha de tráfego com outros Servidores MFA.
   >
   > Não precisa de desinstalar o seu Servidor MFA atual antes de executar o instalador. O instalador realiza uma atualização no local. O percurso de instalação é recolhido a partir do registo da instalação anterior, por isso instala-se no mesmo local (por exemplo, C:\Program Files\Multi-Factor Authentication Server).
  
5. Se for solicitado a instalar um pacote de atualização Redistribuível Microsoft Visual C++ 2015, aceite o pedido. As versões x86 e x64 da embalagem estão instaladas.
6. Se utilizar o SDK do Serviço Web, é-lhe pedido que instale o novo SDK do Serviço Web. Quando instalar o novo Web Service SDK, certifique-se de que o nome de diretório virtual corresponde ao diretório virtual previamente instalado (por exemplo, MultiFactorAuthWebServiceSdk).
7. Repita os passos em todos os servidores subordinados. Promover um dos subordinados para ser o novo mestre, em seguida, atualizar o antigo servidor mestre.

## <a name="upgrade-the-user-portal"></a>Atualizar o Portal do Utilizador

Complete a atualização dos seus Servidores MFA antes de passar para esta secção.

1. Faça uma cópia de segurança do ficheiro web.config que se encontra no diretório virtual da localização de instalação do Portal do Utilizador (por exemplo, C:\inetpub\wwwroot\MultiFactorAuth). Se foram feitas alterações ao tema predefinido, faça uma cópia de segurança da pasta App_Themes\Padrão também. É melhor criar uma cópia da pasta Padrão e criar um novo tema do que alterar o tema Padrão.
2. Se o Portal do Utilizador for executado no mesmo servidor que os outros componentes do Servidor MFA, a instalação do Servidor MFA solicita-lhe que atualize o Portal do Utilizador. Aceite o aviso e instale a atualização do Portal do Utilizador. Verifique se o nome do diretório virtual corresponde ao diretório virtual previamente instalado (por exemplo, MultiFactorAuth).
3. Se o Portal do Utilizador estiver no seu próprio servidor, copie o ficheiro MultiFactorAuthenticationUserPortalSetup64.msi a partir da localização de instalação de um dos Servidores MFA e coloque-o no servidor web do Portal do Utilizador. Execute o instalador.

   Se ocorrer um erro indicando: "Microsoft Visual C++ 2015 Redistribuable Update 1 ou superior é necessário", descarregue e instale o mais recente pacote de atualização do [Microsoft Download Center](https://www.microsoft.com/download/). Instale as versões x86 e x64.

4. Depois de instalado o software portal do utilizador atualizado, compare a cópia de segurança web.config que fez no passo 1 com o novo ficheiro web.config. Se não existirem novos atributos no novo web.config, copie o seu web.config de reserva para o diretório virtual para substituir o novo. Outra opção é copiar/colar os valores das aplicaçõesDefinições e o URL SDK do Serviço Web a partir do ficheiro de backup para o novo web.config.

Se tiver o Portal do Utilizador em vários servidores, repita a instalação em todos eles.

## <a name="upgrade-the-mobile-app-web-service"></a>Atualizar o Serviço Web da Aplicação Móvel

> [!NOTE]
> Ao atualizar a partir de uma versão do Azure MFA Server com idade superior a 8.0 a 8.0+ então o serviço web de aplicação móvel pode ser desinstalado após a atualização

## <a name="upgrade-the-ad-fs-adapters"></a>Atualizar os adaptadores AD FS

Complete a atualização dos seus Servidores MFA e Portal do Utilizador antes de passar para esta secção.

### <a name="if-mfa-runs-on-different-servers-than-ad-fs"></a>Se o MFA funciona em servidores diferentes do AD FS

Estas instruções só se aplicam se executar o Servidor de Autenticação Multifactor separadamente dos seus servidores AD FS. Se ambos os serviços funcionarem nos mesmos servidores, salte esta secção e vá para as etapas de instalação. 

1. Guarde uma cópia do ficheiro MultiFactorAuthenticationAdfsAdapter.config que tenha sido registado em AD FS ou exporte a configuração utilizando o seguinte comando PowerShell: `Export-AdfsAuthenticationProviderConfigurationData -Name [adapter name] -FilePath [path to config file]`. O nome do adaptador é "WindowsAzureMultiFactorAuthentication" ou "AzureMfaServerAuthentication" dependendo da versão anteriormente instalada.
2. Copie os seguintes ficheiros da localização de instalação do Servidor MFA para os servidores AD FS:

   * MultiFactorAuthenticationAdfsAdapterSetup64.msi
   * Register-MultiFactorAuthenticationAdfsAdapter.ps1
   * Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
   * MultiFactorAuthenticationAdfsAdapter.config

3. Editar o script Register-MultiFactorAuthenticationAdfsAdapter.ps1 adicionando `-ConfigurationFilePath [path]` `Register-AdfsAuthenticationProvider` ao final do comando. Substitua *[caminho]* pelo caminho completo para o ficheiro MultiFactorAuthenticationAdfsAdapter.config ou o ficheiro de configuração exportado na etapa anterior.

   Verifique os atributos do novo MultiFactorAuthenticationAdfsAdapter.config para ver se correspondem ao ficheiro config antigo. Se algum atributo foi adicionado ou removido na nova versão, copie os valores do atributo do ficheiro de configuração antigo para o novo ou modifique o ficheiro de configuração antigo para corresponder.

### <a name="install-new-ad-fs-adapters"></a>Instale novos adaptadores AD FS

> [!IMPORTANT]
> Os seus utilizadores não serão obrigados a efetuar uma verificação em duas etapas durante os passos 3-8 desta secção. Se tiver AD FS configurado em vários clusters, pode remover, atualizar e restaurar cada cluster na quinta independentemente dos outros aglomerados para evitar o tempo de inatividade.

1. Remova alguns servidores AD FS da quinta. Atualize estes servidores enquanto os outros ainda estão em execução.
2. Instale o novo adaptador AD FS em cada servidor removido da exploração AD FS. Se o Servidor MFA estiver instalado em cada servidor AD FS, pode atualizar através do administrador do Servidor MFA UX. Caso contrário, atualize através do funcionamento MultiFactorAuthenticationAdfsAdapterSetup64.msi.

   Se ocorrer um erro indicando: "Microsoft Visual C++ 2015 Redistribuable Update 1 ou superior é necessário", descarregue e instale o mais recente pacote de atualização do [Microsoft Download Center](https://www.microsoft.com/download/). Instale as versões x86 e x64.

3. Vá para políticas de**autenticação** >  **AD FS** > Editar Política Global de**Autenticação MultiFactor.** Desmarque **o WindowsAzureMultiFactorAuthentication** ou **o AzureMFAServerAuthentication** (dependendo da versão atual instalada).

   Uma vez concluído este passo, a verificação em duas etapas através do MFA Server não está disponível neste cluster AD FS até completar o passo 8.

4. Desregistre a versão mais antiga do adaptador AD FS executando o script Unregister-MultiFactorAuthenticationAdfsAdapter.ps1 PowerShell. Certifique-se de que o parâmetro *-Nome* (ou "WindowsAzureMultiFactorAuthentication" ou "AzureMFAServerAuthentication") corresponde ao nome apresentado no passo 3. Isto aplica-se a todos os servidores do mesmo cluster AD FS uma vez que existe uma configuração central.
5. Registe o novo adaptador AD FS executando o script Register-MultiFactorAuthenticationAdfsAdapter.ps1 PowerShell. Isto aplica-se a todos os servidores do mesmo cluster AD FS uma vez que existe uma configuração central.
6. Reiniciar o serviço AD FS em cada servidor removido da exploração AD FS.
7. Adicione os servidores atualizados de volta à fazenda AD FS e remova os outros servidores da quinta.
8. Vá para políticas de**autenticação** >  **AD FS** > Editar Política Global de**Autenticação MultiFactor.** Verifique a **autenticação do AzureMfaServer**.
9. Repita o passo 2 para atualizar os servidores agora removidos da exploração AD FS e reiniciar o serviço AD FS nesses servidores.
10. Adicione os servidores de volta na fazenda AD FS.

## <a name="next-steps"></a>Passos seguintes

* Obtenha exemplos de [cenários avançados com autenticação multi-factor Azure e VPNs de terceiros](howto-mfaserver-nps-vpn.md)

* [Sincronizar o Servidor MFA com o Diretório Ativo do Servidor do Windows](howto-mfaserver-dir-ad.md)

* [Configure a autenticação do Windows](howto-mfaserver-windows.md) para as suas aplicações
