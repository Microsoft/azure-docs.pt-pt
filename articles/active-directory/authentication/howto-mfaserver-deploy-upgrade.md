---
title: Atualizando o servidor MFA do Azure-Azure Active Directory
description: Etapas e orientações para atualizar o Servidor de Autenticação Multifator do Azure para uma versão mais recente.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 756c45541907c52448805376e1b054180c31fdf5
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848107"
---
# <a name="upgrade-to-the-latest-azure-multi-factor-authentication-server"></a>Atualizar para a última Servidor de Autenticação Multifator do Azure

Este artigo orienta você pelo processo de atualização do servidor de autenticação multifator do Azure (MFA) v 6.0 ou superior. Se você precisar atualizar uma versão antiga do agente do PhoneFactor, consulte [atualizar o agente do PhoneFactor para o Azure servidor de autenticação multifator](howto-mfaserver-deploy-upgrade-pf.md).

Se você estiver atualizando do v6. x ou mais antigo para o v7. x ou mais recente, todos os componentes serão alterados do .NET 2,0 para o .NET 4,5. Todos os componentes também exigem o C++ Microsoft Visual 2015 redistribuível atualização 1 ou superior. O instalador do servidor MFA instalará as versões x86 e x64 desses componentes se eles ainda não estiverem instalados. Se o portal do usuário e o serviço Web de aplicativo móvel forem executados em servidores separados, você precisará instalar esses pacotes antes de atualizar esses componentes. Você pode procurar a atualização mais recente do C++ Microsoft Visual 2015 redistribuível no [centro de download da Microsoft](https://www.microsoft.com/download/). 

> [!IMPORTANT]
> A partir de 1º de julho de 2019, a Microsoft não oferecerá mais o servidor MFA para novas implantações. Novos clientes que queiram exigir a autenticação multifator de seus usuários devem usar a autenticação multifator do Azure baseada em nuvem. Os clientes existentes que ativaram o servidor MFA antes de 1º de julho poderão baixar a versão mais recente, futuras atualizações e gerar credenciais de ativação como de costume.

Etapas de atualização em um relance:

* Atualizar servidores MFA do Azure (subordinados, em seguida, mestre)
* Atualizar as instâncias do portal do usuário
* Atualizar as instâncias do adaptador de AD FS

## <a name="upgrade-azure-mfa-server"></a>Atualizar o servidor MFA do Azure

1. Use as instruções em [baixar o servidor de autenticação multifator do Azure](howto-mfaserver-deploy.md#download-the-mfa-server) para obter a versão mais recente do instalador do servidor do Azure MFA.
2. Faça um backup do arquivo de dados do servidor MFA localizado em C:\Program Programas\servidor Authentication Server\Data\PhoneFactor.pfdata (supondo o local de instalação padrão) em seu servidor de MFA mestre.
3. Se você executar vários servidores para alta disponibilidade, altere os sistemas cliente que se autenticam no servidor MFA para que eles parem de enviar tráfego para os servidores que estão atualizando. Se você usar um balanceador de carga, remova um servidor MFA subordinado do balanceador de carga, faça a atualização e, em seguida, adicione o servidor de volta ao farm.
4. Execute o novo instalador em cada servidor MFA. Atualize os servidores subordinados primeiro porque eles podem ler o arquivo de dados antigo que está sendo replicado pelo mestre.

   > [!NOTE]
   > Ao atualizar um servidor, ele deve ser removido de qualquer compartilhamento de tráfego ou de balanceamento de carga com outros servidores MFA.
   >
   > Não é necessário desinstalar o servidor MFA atual antes de executar o instalador. O instalador executa uma atualização in-loco. O caminho de instalação é obtido do registro da instalação anterior, portanto, ele é instalado no mesmo local (por exemplo, C:\Program Programas\servidor Authentication Server).
  
5. Se você for solicitado a instalar um pacote de atualização C++ redistribuível do Microsoft Visual 2015, aceite o prompt. As versões x86 e x64 do pacote estão instaladas.
6. Se você usar o SDK do serviço Web, será solicitado que você instale o novo SDK do serviço Web. Quando você instalar o novo SDK do serviço Web, verifique se o nome do diretório virtual corresponde ao diretório virtual instalado anteriormente (por exemplo, MultiFactorAuthWebServiceSdk).
7. Repita as etapas em todos os servidores subordinados. Promova um dos subordinados para ser o novo mestre e, em seguida, atualize o servidor mestre antigo.

## <a name="upgrade-the-user-portal"></a>Atualizar o portal do usuário

Conclua a atualização de seus servidores MFA antes de ir para esta seção.

1. Faça um backup do arquivo Web. config que está no diretório virtual do local de instalação do portal do usuário (por exemplo, C:\inetpub\wwwroot\MultiFactorAuth). Se alguma alteração foi feita no tema padrão, faça um backup da pasta App_Themes \Default também. É melhor criar uma cópia da pasta padrão e criar um novo tema do que alterar o tema padrão.
2. Se o portal do usuário for executado no mesmo servidor que os outros componentes do servidor MFA, a instalação do servidor MFA solicitará que você atualize o portal do usuário. Aceite o prompt e instale a atualização do portal do usuário. Verifique se o nome do diretório virtual corresponde ao diretório virtual instalado anteriormente (por exemplo, MultiFactorAuth).
3. Se o portal do usuário estiver em seu próprio servidor, copie o arquivo MultiFactorAuthenticationUserPortalSetup64. msi do local de instalação de um dos servidores MFA e coloque-o no servidor Web do portal do usuário. Execute o instalador.

   Se ocorrer um erro informando que " C++ Microsoft Visual 2015 redistribuível atualização 1 ou superior é necessário", baixe e instale o pacote de atualização mais recente no [centro de download da Microsoft](https://www.microsoft.com/download/). Instale as versões x86 e x64.

4. Após a instalação do software do portal do usuário atualizado, compare o backup do Web. config que você fez na etapa 1 com o novo arquivo Web. config. Se não existirem novos atributos no novo Web. config, copie o Web. config de backup no diretório virtual para substituir o novo. Outra opção é copiar/colar os valores appSettings e a URL do SDK do serviço Web do arquivo de backup para o novo Web. config.

Se você tiver o portal do usuário em vários servidores, repita a instalação em todos eles.

## <a name="upgrade-the-mobile-app-web-service"></a>Atualizar o serviço Web de aplicativos móveis

> [!NOTE]
> Ao atualizar de uma versão do servidor MFA do Azure com mais de 8,0 para 8.0 +, o serviço Web de aplicativos móveis pode ser desinstalado após a atualização

## <a name="upgrade-the-ad-fs-adapters"></a>Atualizar os adaptadores de AD FS

Conclua a atualização dos seus servidores MFA e do portal do usuário antes de passar para esta seção.

### <a name="if-mfa-runs-on-different-servers-than-ad-fs"></a>Se a MFA for executada em servidores diferentes do que AD FS

Essas instruções se aplicam somente se você executar Servidor de Autenticação Multifator separadamente de seus servidores de AD FS. Se ambos os serviços forem executados nos mesmos servidores, pule esta seção e vá para as etapas de instalação. 

1. Salve uma cópia do arquivo MultiFactorAuthenticationAdfsAdapter. config que foi registrado no AD FS ou exporte a configuração usando o seguinte comando do PowerShell: `Export-AdfsAuthenticationProviderConfigurationData -Name [adapter name] -FilePath [path to config file]`. O nome do adaptador é "WindowsAzureMultiFactorAuthentication" ou "AzureMfaServerAuthentication", dependendo da versão instalada anteriormente.
2. Copie os seguintes arquivos do local de instalação do servidor MFA para os servidores de AD FS:

   * MultiFactorAuthenticationAdfsAdapterSetup64.msi
   * Register-MultiFactorAuthenticationAdfsAdapter.ps1
   * Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
   * MultiFactorAuthenticationAdfsAdapter.config

3. Edite o script Register-multifactorauthenticationadfsadapter. ps1 adicionando `-ConfigurationFilePath [path]` ao final do comando `Register-AdfsAuthenticationProvider`. Substitua *[Path]* pelo caminho completo do arquivo MultiFactorAuthenticationAdfsAdapter. config ou do arquivo de configuração exportado na etapa anterior.

   Verifique os atributos no novo MultiFactorAuthenticationAdfsAdapter. config para ver se eles correspondem ao arquivo de configuração antigo. Se algum atributo tiver sido adicionado ou removido na nova versão, copie os valores de atributo do arquivo de configuração antigo para o novo ou modifique o arquivo de configuração antigo para corresponder.

### <a name="install-new-ad-fs-adapters"></a>Instalar novos adaptadores de AD FS

> [!IMPORTANT]
> Os usuários não serão obrigados a executar a verificação em duas etapas durante as etapas de 3-8 desta seção. Se você tiver AD FS configurado em vários clusters, poderá remover, atualizar e restaurar cada cluster no farm independentemente dos outros clusters para evitar o tempo de inatividade.

1. Remova alguns servidores AD FS do farm. Atualize esses servidores enquanto os outros ainda estiverem em execução.
2. Instale o novo adaptador de AD FS em cada servidor removido do farm de AD FS. Se o servidor MFA estiver instalado em cada servidor de AD FS, você poderá atualizar por meio do UX de administração do servidor MFA. Caso contrário, atualize executando MultiFactorAuthenticationAdfsAdapterSetup64. msi.

   Se ocorrer um erro informando que " C++ Microsoft Visual 2015 redistribuível atualização 1 ou superior é necessário", baixe e instale o pacote de atualização mais recente no [centro de download da Microsoft](https://www.microsoft.com/download/). Instale as versões x86 e x64.

3. Vá para **AD FS** > **políticas de autenticação** > **Editar a política de autenticação multifator global**. Desmarque **WindowsAzureMultiFactorAuthentication** ou **AzureMFAServerAuthentication** (dependendo da versão atual instalada).

   Após a conclusão desta etapa, a verificação em duas etapas por meio do servidor MFA não estará disponível neste AD FS cluster até que você conclua a etapa 8.

4. Cancele o registro da versão mais antiga do adaptador de AD FS executando o script do PowerShell Unregister-multifactorauthenticationadfsadapter. ps1. Verifique se o parâmetro *-Name* ("WindowsAzureMultiFactorAuthentication" ou "AzureMFAServerAuthentication") corresponde ao nome que foi exibido na etapa 3. Isso se aplica a todos os servidores no mesmo cluster AD FS, pois há uma configuração central.
5. Registre o novo adaptador de AD FS executando o script do PowerShell Register-multifactorauthenticationadfsadapter. ps1. Isso se aplica a todos os servidores no mesmo cluster AD FS, pois há uma configuração central.
6. Reinicie o serviço AD FS em cada servidor removido do farm de AD FS.
7. Adicione os servidores atualizados de volta ao farm de AD FS e remova os outros servidores do farm.
8. Vá para **AD FS** > **políticas de autenticação** > **Editar a política de autenticação multifator global**. Verifique **AzureMfaServerAuthentication**.
9. Repita a etapa 2 para atualizar os servidores agora removidos do farm de AD FS e reinicie o serviço de AD FS nesses servidores.
10. Adicione esses servidores de volta ao farm de AD FS.

## <a name="next-steps"></a>Passos seguintes

* Obtenha exemplos de [cenários avançados com a autenticação multifator do Azure e VPNs de](howto-mfaserver-nps-vpn.md) terceiros

* [Sincronizar servidor MFA com o Windows Server Active Directory](howto-mfaserver-dir-ad.md)

* [Configurar a autenticação do Windows](howto-mfaserver-windows.md) para seus aplicativos
