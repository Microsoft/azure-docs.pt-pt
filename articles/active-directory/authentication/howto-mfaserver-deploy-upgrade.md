---
title: Atualizar servidor MFA do Azure - o Azure Active Directory
description: Passos e documentação de orientação para atualizar o servidor do Azure multi-factor Authentication para uma versão mais recente.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c01c7a22800d633696382687feb7090a4ed8b60
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58315666"
---
# <a name="upgrade-to-the-latest-azure-multi-factor-authentication-server"></a>Atualizar para o servidor de autenticação de multi-factor do Azure mais recente

Este artigo orienta-o processo de atualização do servidor multi-factor Authentication (MFA) do Azure v6.0 ou superior. Se precisar de atualizar uma versão antiga do PhoneFactor Agent, consulte [atualizar o PhoneFactor Agent para o servidor do Azure multi-factor Authentication](howto-mfaserver-deploy-upgrade-pf.md).

Se estiver a atualizar a partir de v6.x ou mais antigo para o v7.x ou mais recente, alterar todos os componentes do .NET 2.0 para o .NET 4.5. Todos os componentes também requerem o Microsoft Visual C++ 2015 a atualização redistribuível 1 ou superior. O instalador do servidor MFA instala as versões x86 e x64 desses componentes, se ainda não estiverem instalados. Se o Portal de utilizador e o serviço Web da aplicação móvel são executados em servidores separados, terá de instalar os pacotes antes de atualizar esses componentes. Pode pesquisar para a atualização mais recente do Microsoft Visual C++ 2015 Redistributable sobre o [Microsoft Download Center](https://www.microsoft.com/download/). 

Etapas de atualização rapidamente:

* Atualizar os servidores MFA do Azure (subordinados, em seguida, Master)
* Atualizar as instâncias do Portal de utilizador
* Atualizar as instâncias do adaptador AD FS

## <a name="upgrade-azure-mfa-server"></a>Atualizar o servidor MFA do Azure

1. Utilize as instruções em [transferir o servidor do Azure multi-factor Authentication](howto-mfaserver-deploy.md#download-the-mfa-server) para obter a versão mais recente do programa de instalação do servidor MFA do Azure.
2. Fazer uma cópia de segurança o ficheiro de dados do servidor MFA localizada em C:\Program multi-Factor Authentication Server\Data\PhoneFactor.pfdata (supondo que a localização de instalação predefinida) no seu servidor de MFA mestre.
3. Se executar vários servidores para elevada disponibilidade, altere os sistemas de cliente que se autenticar no servidor de MFA, para que eles param o envio de tráfego para os servidores que estiver a atualizar. Se utilizar um balanceador de carga, remover um servidor de MFA subordinados do Balanceador de carga, a atualização e, em seguida, adicione o servidor de volta para o farm.
4. Execute o instalador de novo em cada servidor de MFA. Atualize servidores subordinados primeiro porque podem ler o arquivo de dados antigo a ser replicado pelo mestre de.

   > [!NOTE]
   > Ao atualizar um servidor devem ser removido de qualquer balanceamento de carga ou o tráfego de partilha com outros servidores de MFA.
   >
   > Não é necessário desinstalar o servidor de MFA atual antes de executar o instalador. O instalador executa uma atualização no local. O caminho de instalação é escolhido do registo da instalação anterior, para que ele é instalado na mesma localização (por exemplo, c:\Programas\Microsoft files\servidor multi-Factor Authentication Server).
  
5. Se lhe for pedido para instalar um pacote de atualização do Microsoft Visual C++ 2015 Redistributable, aceite o pedido. Ambas as versões x86 e x64 do pacote são instaladas.
6. Se utilizar o SDK do serviço Web, lhe for pedido para instalar o novo Web Service SDK. Quando instala o novo Web Service SDK, certifique-se de que o nome de diretório virtual corresponde o diretório virtual previamente instalado (por exemplo, MultiFactorAuthWebServiceSdk).
7. Repita os passos em todos os servidores subordinados. Promova um dos subordinados para ser o novo mestre, em seguida, atualize o servidor de mestra antiga.

## <a name="upgrade-the-user-portal"></a>Atualizar o Portal de utilizador

Conclua a atualização dos seus servidores MFA antes de passar a esta secção.

1. Fazer uma cópia de segurança do ficheiro Web. config que se encontra no diretório virtual da localização de instalação do Portal de utilizador (por exemplo, C:\inetpub\wwwroot\MultiFactorAuth). Se todas as alterações foram feitas para o tema predefinido, fazer uma cópia de segurança da pasta App_Themes\Default. É melhor criar uma cópia da pasta predefinida e criar um novo tema de que para alterar o tema predefinido.
2. Se o Portal de utilizador é executado no mesmo servidor que os outros componentes de servidor MFA, a instalação do servidor de MFA solicita que atualizar o Portal de utilizador. Aceitar o pedido e instalar a atualização do Portal de utilizador. Verifique se o nome do diretório virtual corresponde ao diretório virtual previamente instalado (por exemplo, MultiFactorAuth).
3. Se o Portal de utilizador estiver no seu próprio servidor, copie o ficheiro de MultiFactorAuthenticationUserPortalSetup64.msi da localização de instalação de um dos servidores MFA e colocá-la para o servidor de web do Portal de utilizador. Execute o instalador.

   Se ocorrer que um erro, "Microsoft Visual C++ Redistributable atualização 2015 1 ou superior" é necessária, transfira e instale o pacote de atualização mais recente do [Microsoft Download Center](https://www.microsoft.com/download/). Instale as versões x86 e x64.

4. Depois do software atualizado do Portal de utilizador estiver instalado, compare a cópia de segurança de Web. config que fez no passo 1 com o novo ficheiro Web. config. Não se existirem nenhuma novos atributos ao novo Web. config, copie a cópia de segurança Web. config para o diretório virtual para substituir o novo. Outra opção consiste em copiar/colar os valores de appSettings e o URL do SDK do serviço Web do ficheiro de cópia de segurança para o novo Web. config.

Se tiver o Portal de utilizador em vários servidores, repita a instalação em todos eles.

## <a name="upgrade-the-mobile-app-web-service"></a>Atualizar o serviço Web da aplicação móvel

> [!NOTE]
> Ao atualizar de uma versão do Servidor MFA do Azure anterior à 8.0 até à 8.0+ que o serviço Web da aplicação móvel pode ser desinstalado após a atualização

## <a name="upgrade-the-ad-fs-adapters"></a>Atualizar os adaptadores do AD FS

Conclua a atualização dos seus servidores MFA e o Portal de utilizador antes de passar a esta secção.

### <a name="if-mfa-runs-on-different-servers-than-ad-fs"></a>Se a MFA é executado em servidores diferentes que do AD FS

Estas instruções aplicam-se apenas se executar o servidor multi-factor Authentication em separado dos seus servidores do AD FS. Se ambos os serviços são executados em servidores do mesmo, ignore esta secção e avance para os passos de instalação. 

1. Guardar uma cópia do ficheiro Multifactorauthenticationadfsadapter config que foi registado no AD FS ou exportar a configuração com o seguinte comando do PowerShell: `Export-AdfsAuthenticationProviderConfigurationData -Name [adapter name] -FilePath [path to config file]`. O nome do adaptador é "WindowsAzureMultiFactorAuthentication" ou "AzureMfaServerAuthentication" consoante a versão instalada anteriormente.
2. Copie os seguintes ficheiros a partir da localização de instalação do servidor MFA para os servidores do AD FS:

   * MultiFactorAuthenticationAdfsAdapterSetup64.msi
   * Register-MultiFactorAuthenticationAdfsAdapter.ps1
   * Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
   * MultiFactorAuthenticationAdfsAdapter.config

3. Editar o script Register-MultiFactorAuthenticationAdfsAdapter.ps1 adicionando `-ConfigurationFilePath [path]` no final do `Register-AdfsAuthenticationProvider` comando. Substitua *[caminho]* com o caminho completo para o multifactorauthenticationadfsadapter. config, ficheiro ou o ficheiro de configuração exportado no passo anterior.

   Verifique os atributos no novo multifactorauthenticationadfsadapter. config para ver se eles correspondem o arquivo de configuração antigo. Se todos os atributos foram adicionados ou removidos na nova versão, copie os valores de atributo do arquivo de configuração antiga para a nova ou modificar o arquivo de configuração antigo para corresponder.

### <a name="install-new-ad-fs-adapters"></a>Instalar novos adaptadores do AD FS

> [!IMPORTANT]
> Os utilizadores não serão necessário para efetuar a verificação de dois passos durante os passos 3-8 desta secção. Se tiver configurado em vários clusters do AD FS, pode remover, atualizar e restaurar cada cluster do farm independentemente dos outros clusters para evitar o tempo de inatividade.

1. Remova alguns servidores do AD FS do farm. Atualize estes servidores, enquanto os outros ainda estão em execução.
2. Instale o adaptador AD FS novo em cada servidor removido do farm do AD FS. Se o servidor de MFA está instalado em cada servidor do AD FS, pode atualizar através do administrador do servidor MFA experiência do usuário. Caso contrário, atualize, executando MultiFactorAuthenticationAdfsAdapterSetup64.msi.

   Se ocorrer que um erro, "Microsoft Visual C++ Redistributable atualização 2015 1 ou superior" é necessária, transfira e instale o pacote de atualização mais recente do [Microsoft Download Center](https://www.microsoft.com/download/). Instale as versões x86 e x64.

3. Aceda a **do AD FS** > **políticas de autenticação** > **Editar política de autenticação Multifator Global**. Desmarque **WindowsAzureMultiFactorAuthentication** ou **AzureMFAServerAuthentication** (consoante a versão atual instalada).

   Uma vez concluído este passo, verificação de dois passos através do servidor MFA não está disponível neste cluster de AD FS, até concluir o passo 8.

4. Anular o registo a versão mais antiga do adaptador AD FS, executando o script do PowerShell Unregister-MultiFactorAuthenticationAdfsAdapter.ps1. Certifique-se de que o *-nome* parâmetro ("WindowsAzureMultiFactorAuthentication" ou "AzureMFAServerAuthentication") corresponde ao nome que foi apresentado no passo 3. Isto aplica-se a todos os servidores no mesmo cluster do AD FS, uma vez que existe uma configuração central.
5. Executar o script Register-MultiFactorAuthenticationAdfsAdapter.ps1 PowerShell para registar o adaptador AD FS novo. Isto aplica-se a todos os servidores no mesmo cluster do AD FS, uma vez que existe uma configuração central.
6. Reinicie o serviço do AD FS em cada servidor removido do farm do AD FS.
7. Adicionar os servidores atualizados para o farm do AD FS e remova os outros servidores do farm.
8. Aceda a **do AD FS** > **políticas de autenticação** > **Editar política de autenticação Multifator Global**. Check **AzureMfaServerAuthentication**.
9. Repita o passo 2 para atualizar os servidores foi removidos do farm do AD FS e reinicie o serviço do AD FS nesses servidores.
10. Adicione esses servidores no farm do AD FS.

## <a name="next-steps"></a>Passos Seguintes

* Obter exemplos de [cenários com multi-factor Authentication do Azure e VPNs de terceiros](howto-mfaserver-nps-vpn.md)

* [Sincronizar o servidor MFA com Windows Server Active Directory](howto-mfaserver-dir-ad.md)

* [Configurar a autenticação do Windows](howto-mfaserver-windows.md) para as suas aplicações
