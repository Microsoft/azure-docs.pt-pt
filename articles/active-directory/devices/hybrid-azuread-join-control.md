---
title: Validação controlada da ad ida híbrida Azure - Azure AD
description: Aprenda a fazer uma validação controlada do Híbrido Azure AD aderir antes de permitir que em toda a organização seja de uma só vez
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: f43db805ccbb7d4e546c51bbe39350f4bbba2efb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049984"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>Validação controlada de associação do Azure AD híbrido

Quando todos os pré-requisitos estiverem em vigor, os dispositivos Windows registar-se-ão automaticamente como dispositivos no seu inquilino Azure AD. O estado destas identidades de dispositivo em Azure AD é referido como a adesão híbrida azure AD. Mais informações sobre os conceitos abrangidos por este artigo podem ser encontradas nos artigos Introdução à gestão de [dispositivos no Diretório Ativo do Azure](overview.md) e planear a implementação do [seu Diretório Ativo Azure híbrido.](hybrid-azuread-join-plan.md)

As organizações podem querer fazer uma validação controlada do AD Híbrido Azure antes de permitir em toda a sua organização tudo de uma vez. Este artigo irá explicar como realizar uma validação controlada da adesão híbrida azure AD.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>Validação controlada de AD híbrido Azure juntam-se aos dispositivos atuais do Windows

Para dispositivos que executam o sistema operativo Windows desktop, a versão suportada é a Atualização de Aniversário do Windows 10 (versão 1607) ou posterior. Como uma melhor prática, atualize para a versão mais recente do Windows 10.

Para fazer uma validação controlada do AD Híbrido Azure, junte-se aos dispositivos atuais do Windows, é necessário:

1. Limpar a entrada do Ponto de Ligação de Serviço (SCP) do Diretório Ativo (AD) se existir
1. Configure a definição de registo do lado do cliente para sCP nos seus computadores unidos pelo domínio usando um Objeto de Política de Grupo (GPO)
1. Se estiver a utilizar AD FS, também deve configurar a definição de registo do lado do cliente para SCP no seu servidor AD FS utilizando um GPO  
1. Também pode ser necessário [personalizar opções](../hybrid/how-to-connect-post-installation.md#additional-tasks-available-in-azure-ad-connect) de sincronização no Azure AD Connect para permitir a sincronização do dispositivo. 


### <a name="clear-the-scp-from-ad"></a>Limpar o SCP da AD

Utilize o Editor de Interfaces de Diretório Ativo (ADSI Edit) para modificar os objetos SCP em AD.

1. Lance a aplicação de desktop **ADSI Edit** a partir de uma estação de trabalho administrativa ou um controlador de domínio como Administrador da Empresa.
1. Ligue-se ao contexto de nomeação de **configuração** do seu domínio.
1. Navegue para **CN=Configuração,DC=contoso,DC=com** > **CN=Services** > **CN=Device Registration Configuration**
1. Clique à direita no objeto de folha **CN=62a0ff2e-97b9-4513-943f-0d221bd30080** e selecione **Properties**
   1. Selecione **palavras-chave** da janela **Do Atributo Editor** e clique em **Editar**
   1. Selecione os valores de **azureADId** e **azureADName** (um de cada vez) e clique em **Remover**
1. Edição de **ADSI**


### <a name="configure-client-side-registry-setting-for-scp"></a>Configurar a definição de registo do lado do cliente para o SCP

Utilize o exemplo seguinte para criar um Objeto de Política de Grupo (GPO) para implementar uma definição de registo configurando uma entrada SCP no registo dos seus dispositivos.

1. Abra uma consola de Gestão de Políticas de Grupo e crie um novo Objeto político de grupo no seu domínio.
   1. Forneça um nome ao seu GPO recém-criado (por exemplo, ClientSideSCP).
1. Editar o GPO e localizar o seguinte caminho: **Configuração** > de computador**Preferências** > Registo**de** **Definições** > do Windows
1. Clique à direita no Registo e selecione **Novo** > **Item** de Registo
   1. No separador **Geral,** configure o seguinte
      1. Ação: **Atualização**
      1. Colmeia: **HKEY_LOCAL_MACHINE**
      1. Caminho-chave: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Nome de valor: **TenantId**
      1. Tipo de valor: **REG_SZ**
      1. Dados de valor: O ID GUID ou **Diretório** da sua instância AD Azure (este valor pode ser encontrado no id > de**diretório**de**propriedades** > ativas do > portal **Azure****Azure)**
   1. Clique **OK**
1. Clique à direita no Registo e selecione **Novo** > **Item** de Registo
   1. No separador **Geral,** configure o seguinte
      1. Ação: **Atualização**
      1. Colmeia: **HKEY_LOCAL_MACHINE**
      1. Caminho-chave: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Nome de valor: **Nome do inquilino**
      1. Tipo de valor: **REG_SZ**
      1. Dados de valor: O **seu nome** de domínio verificado se estiver a utilizar ambientefederado, como AD FS. O seu nome de **domínio** verificado ou `contoso.onmicrosoft.com` o seu nome de domínio onmicrosoft.com, por exemplo, se estiver a utilizar um ambiente gerido
   1. Clique **OK**
1. Feche o editor para o gpo recém-criado
1. Ligue o GPO recém-criado ao om desejado contendo computadores unidos pelo domínio que pertencem à sua população de lançamento controlada

### <a name="configure-ad-fs-settings"></a>Configurar as definições de FS AD

Se estiver a utilizar AD FS, primeiro precisa de configurar o SCP do lado do cliente utilizando as instruções acima mencionadas, ligando o GPO aos seus servidores AD FS. O objeto SCP define a fonte de autoridade para objetos de dispositivo. Pode ser no local ou azure d.D. Quando o SCP do lado do cliente é configurado para AD FS, a fonte para objetos do dispositivo é estabelecida como Azure AD.

> [!NOTE]
> Se não configurar o SCP do lado do cliente nos seus servidores AD FS, a fonte de identidades do dispositivo seria considerada como no local. A ADFS iniciará então a desposição de objetos do dispositivo a partir do diretório no local após o período estipulado definido no atributo "MaximumInactiveDays" do Registo de Dispositivos ADFS. Dispositivo ADFS Os objetos de registo podem ser encontrados utilizando o [cmdlet Get-AdfsDeviceRegistration](/powershell/module/adfs/get-adfsdeviceregistration?view=win10-ps).

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>Validação controlada de AD híbrido Azure juntam-se a dispositivos de nível inferior windows

Para registar dispositivos de nível inferior do Windows, as organizações devem instalar o [Microsoft Workplace Join para computadores não Windows 10](https://www.microsoft.com/download/details.aspx?id=53554) disponíveis no Microsoft Download Center.

Pode implementar o pacote utilizando um sistema de distribuição de software como o [Microsoft Endpoint Configuration Manager](/configmgr/). O pacote suporta as opções de instalação silenciosa padrão com o parâmetro silencioso. O atual ramo do Gestor de Configuração oferece benefícios em versões anteriores, como a capacidade de rastrear registos concluídos.

O instalador cria uma tarefa programada no sistema que funciona no contexto do utilizador. A tarefa é desencadeada quando o utilizador faz o sinal de si no Windows. A tarefa junta-se silenciosamente ao dispositivo com a Azure AD com as credenciais do utilizador após a autenticação com a Azure AD.

Para controlar o registo do dispositivo, deverá utilizar o pacote do Instalador do Windows para o seu grupo selecionado de dispositivos de nível inferior windows.

> [!NOTE]
> Se um SCP não estiver configurado em AD, então deve seguir a mesma abordagem descrita para configurar a definição de registo do [lado do cliente para SCP](#configure-client-side-registry-setting-for-scp)nos seus computadores unidos pelo domínio utilizando um Objeto de Política de Grupo (GPO).


Depois de verificar se tudo funciona como esperado, pode registar automaticamente os restantes dispositivos atuais e de nível inferior do Windows com a AD Azure configurando o [SCP utilizando o Azure AD Connect](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join).

## <a name="next-steps"></a>Passos seguintes

[Planeie o seu Diretório Ativo Azure híbrido aderir à implementação](hybrid-azuread-join-plan.md)
