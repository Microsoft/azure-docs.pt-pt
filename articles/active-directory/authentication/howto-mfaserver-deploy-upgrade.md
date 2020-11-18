---
title: Upgrade Azure MFA Server - Azure Ative Directory
description: Passos e orientação para atualizar o Azure Multi-Factor Authentication Server para uma versão mais recente.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/12/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53f7b0877c1b816bd41226f9207f7dc950eadfd1
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94838523"
---
# <a name="upgrade-to-the-latest-azure-multi-factor-authentication-server"></a>Atualização para o Servidor Multi-Factor Authentication do Azure mais recente

Este artigo acompanha-o através do processo de atualização do Servidor de Autenticação Multi-Factor (MFA) V6.0 ou superior. Se necessitar de atualizar uma versão antiga do Agente PhoneFactor, consulte [o Upgrade do Agente PhoneFactor para O Servidor de Autenticação Multi-Factor Azure](howto-mfaserver-deploy-upgrade-pf.md).

Se estiver a atualizar de v6.x ou mais para v7.x ou mais recente, todos os componentes mudam de .NET 2.0 para .NET 4.5. Todos os componentes também requerem Microsoft Visual C++ 2015 Redistributable Update 1 ou superior. O instalador do MFA Server instala as versões x86 e x64 destes componentes se ainda não estiverem instalados. Se o Portal do Utilizador e o Serviço Web de Aplicações Móveis funcionarem em servidores separados, é necessário instalar esses pacotes antes de atualizar esses componentes. Pode pesquisar a mais recente atualização redistribuível do Microsoft Visual C++ 2015 no [Microsoft Download Center](https://www.microsoft.com/download/). 

> [!IMPORTANT]
> A partir de 1 de julho de 2019, a Microsoft já não oferece O MFA Server para novas implementações. Os novos clientes que pretendam exigir a autenticação de vários fatores (MFA) durante os eventos de entrada devem utilizar a autenticação multi-factor Azure AD baseada na nuvem.
>
> Para começar com mFA baseado na nuvem, consulte [Tutorial: Secure user in events with Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Os clientes existentes que ativaram o MFA Server antes de 1 de julho de 2019 podem descarregar a versão mais recente, futuras atualizações e gerar credenciais de ativação como de costume.

Atualizar passos num ápice:

* Atualizar servidores Azure MFA (subordinados e depois Mestre)
* Atualizar as instâncias do Portal do Utilizador
* Atualizar as instâncias do adaptador AD FS

## <a name="upgrade-azure-mfa-server"></a>Atualizar servidor Azure MFA

1. Utilize as instruções no [Download do Azure Multi-Factor Authentication Server](howto-mfaserver-deploy.md#download-the-mfa-server) para obter a versão mais recente do instalador do Servidor Azure MFA.
2. Faça uma cópia de segurança do ficheiro de dados do Servidor MFA localizado em C:\Program Files\Servidor de Autenticação multi-factor\Data\PhoneFactor.pfdata (assumindo a localização de instalação predefinido) no seu servidor principal do MFA.
3. Se executar vários servidores para uma elevada disponibilidade, altere os sistemas de clientes que autenticam para o Servidor MFA para que deixem de enviar tráfego para os servidores que estão a atualizar. Se utilizar um equilibrador de carga, remova um Servidor MFA subordinado do equilibrador de carga, faça a atualização e, em seguida, adicione o servidor de volta à quinta.
4. Execute o novo instalador em cada Servidor MFA. Atualizar os servidores subordinados primeiro porque podem ler o antigo ficheiro de dados que está a ser replicado pelo mestre.

   > [!NOTE]
   > Ao atualizar um servidor, deve ser removido de qualquer equilíbrio de carga ou partilha de tráfego com outros Servidores MFA.
   >
   > Não é necessário desinstalar o atual Servidor MFA antes de executar o instalador. O instalador executa uma atualização no local. O caminho de instalação é recolhido a partir do registo da instalação anterior, pelo que se instala no mesmo local (por exemplo, C:\Program Files\Multi-Factor Authentication Server).
  
5. Se for solicitado que instale um pacote de atualização redistribuível Microsoft Visual C++ 2015, aceite a solicitação. As versões x86 e x64 do pacote estão instaladas.
6. Se utilizar o Web Service SDK, é-lhe pedido que instale o novo Serviço Web SDK. Quando instalar o novo Web Service SDK, certifique-se de que o nome do diretório virtual corresponde ao diretório virtual previamente instalado (por exemplo, MultiFactorAuthWebServiceSdk).
7. Repita os passos em todos os servidores subordinados. Promover um dos subordinados para ser o novo mestre, em seguida, atualizar o antigo servidor principal.

## <a name="upgrade-the-user-portal"></a>Atualizar o Portal do Utilizador

Complete a atualização dos seus Servidores MFA antes de passar para esta secção.

1. Faça uma cópia de segurança do ficheiro web.config que se encontra no diretório virtual da localização de instalação do Portal do Utilizador (por exemplo, C:\inetpub\wwwroot\MultiFactorAuth). Se foram efetuadas alterações ao tema predefinido, faça também uma cópia de segurança da pasta App_Themes\Predefinido. É melhor criar uma cópia da pasta Padrão e criar um novo tema do que alterar o tema Padrão.
2. Se o Portal do Utilizador funciona no mesmo servidor que os outros componentes do MFA Server, a instalação do MFA Server solicita-lhe que atualize o Portal do Utilizador. Aceite o pedido e instale a atualização do Portal do Utilizador. Verifique se o nome do diretório virtual corresponde ao diretório virtual previamente instalado (por exemplo, MultiFactorAuth).
3. Se o Portal do Utilizador estiver no seu próprio servidor, copie o ficheiro MultiFactorAuthenticationUserPortalSetup64.msi a partir da localização de instalação de um dos Servidores MFA e coloque-o no servidor web do Portal do Utilizador. Execute o instalador.

   Se ocorrer um erro indicando: "Microsoft Visual C++ 2015 Redistributable Update 1 ou superior é necessário", descarregue e instale o mais recente pacote de atualização do [Microsoft Download Center](https://www.microsoft.com/download/). Instale as versões x86 e x64.

4. Depois de instalado o software do Portal do Utilizador atualizado, compare a cópia de segurança web.config que fez no passo 1 com o novo ficheiro web.config. Se não existirem novos atributos no novo web.config, copie o seu web.config de backup no diretório virtual para substituir o novo. Outra opção é copiar/colar os valores de appSettings e o URL SDK do Serviço Web do ficheiro de cópia de segurança para o novo web.config.

Se tiver o Portal do Utilizador em vários servidores, repita a instalação em todos eles.

## <a name="upgrade-the-mobile-app-web-service"></a>Atualizar o Serviço Web de Aplicações Móveis

> [!NOTE]
> Ao atualizar a partir de uma versão do Azure MFA Server com mais de 8.0 a 8.0+ então o serviço web de aplicações móveis pode ser desinstalado após a atualização

## <a name="upgrade-the-ad-fs-adapters"></a>Atualizar os adaptadores AD FS

Complete a atualização dos seus Servidores MFA e portal do utilizador antes de passar para esta secção.

### <a name="if-mfa-runs-on-different-servers-than-ad-fs"></a>Se o MFA funciona em servidores diferentes do AD FS

Estas instruções só se aplicam se executar o Servidor de Autenticação Multi-Factor separadamente dos servidores AD FS. Se ambos os serviços funcionarem nos mesmos servidores, salte esta secção e vá para os passos de instalação. 

1. Guarde uma cópia do ficheiro MultiFactorAuthenticationAdfsAdapter.config registado em FS AD ou exporte a configuração utilizando o seguinte comando PowerShell: `Export-AdfsAuthenticationProviderConfigurationData -Name [adapter name] -FilePath [path to config file]` . O nome do adaptador é "WindowsAzureMultiFactorAuthentication" ou "AzureMfaServerAuthentication" dependendo da versão previamente instalada.
2. Copie os seguintes ficheiros da instalação do MFA Server para os servidores AD FS:

   * MultiFactorAuthenticationAdfsAdapterSetup64.msi
   * Register-MultiFactorAuthenticationAdfsAdapter.ps1
   * Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
   * MultiFactorAuthenticationAdfsAdapter.config

3. Edite o Register-MultiFactorAuthenticationAdfsAdapter.ps1 script adicionando `-ConfigurationFilePath [path]` ao fim do `Register-AdfsAuthenticationProvider` comando. Substitua *[caminho]* pelo caminho completo para o ficheiro MultiFactorAuthenticationAdfsAdapter.config ou pelo ficheiro de configuração exportado no passo anterior.

   Verifique os atributos na nova MultiFactorAuthenticationAdfsAdapter.config para ver se correspondem ao antigo ficheiro config. Se quaisquer atributos foram adicionados ou removidos na nova versão, copie os valores do atributo do antigo ficheiro de configuração para o novo ou modifique o ficheiro de configuração antigo para corresponder.

### <a name="install-new-ad-fs-adapters"></a>Instalar novos adaptadores AD FS

> [!IMPORTANT]
> Os seus utilizadores não serão obrigados a efetuar a verificação em duas etapas durante os passos 3-8 desta secção. Se tiver O FS AD configurado em múltiplos clusters, pode remover, atualizar e restaurar cada cluster na quinta independentemente dos outros aglomerados para evitar tempo de inatividade.

1. Remova alguns servidores AD FS da quinta. Atualize estes servidores enquanto os outros ainda estão em execução.
2. Instale o novo adaptador AD FS em cada servidor removido da quinta AD FS. Se o Servidor MFA estiver instalado em cada servidor AD FS, pode atualizar através do administrador do MFA Server UX. Caso contrário, atualize executando MultiFactorAuthenticationAdfsAdapterSetup64.msi.

   Se ocorrer um erro indicando: "Microsoft Visual C++ 2015 Redistributable Update 1 ou superior é necessário", descarregue e instale o mais recente pacote de atualização do [Microsoft Download Center](https://www.microsoft.com/download/). Instale as versões x86 e x64.

3. Vá para políticas de autenticação **AD FS**  >  **Authentication Policies**  >  **Editar a Política Global de Autenticação MultiFactor**. Desmarque **o WindowsAzureMultiFactorAuthentication** ou **AzureMFAServerAuthentication** (dependendo da versão atual instalada).

   Uma vez concluído este passo, a verificação em duas etapas através do MFA Server não estará disponível neste cluster AD FS até completar o passo 8.

4. Desagregar a versão mais antiga do adaptador AD FS executando o Unregister-MultiFactorAuthenticationAdfsAdapter.ps1 script PowerShell. Certifique-se de que o parâmetro *-Nome* ("WindowsAzureMultiFactorAuthentication" ou "AzureMFAServerAuthentication") corresponde ao nome que foi apresentado no passo 3. Isto aplica-se a todos os servidores do mesmo cluster AD FS, uma vez que existe uma configuração central.
5. Registe o novo adaptador AD FS executando o Register-MultiFactorAuthenticationAdfsAdapter.ps1 script PowerShell. Isto aplica-se a todos os servidores do mesmo cluster AD FS, uma vez que existe uma configuração central.
6. Reinicie o serviço AD FS em cada servidor removido da fazenda AD FS.
7. Adicione os servidores atualizados de volta à fazenda AD FS e remova os outros servidores da quinta.
8. Vá para políticas de autenticação **AD FS**  >  **Authentication Policies**  >  **Editar a Política Global de Autenticação MultiFactor**. Verifique **a AzureMfaServerAuthentication**.
9. Repita o passo 2 para atualizar os servidores agora removidos da fazenda AD FS e reinicie o serviço AD FS nesses servidores.
10. Adicione os servidores de volta à fazenda AD FS.

## <a name="next-steps"></a>Passos seguintes

* Obtenha exemplos de [cenários avançados com autenticação multi-factor Azure e VPNs de terceiros](howto-mfaserver-nps-vpn.md)

* [Sincronizar o servidor MFA com o Diretor ativo do Servidor do Windows Server](howto-mfaserver-dir-ad.md)

* [Configurar autenticação do Windows](howto-mfaserver-windows.md) para as suas aplicações
