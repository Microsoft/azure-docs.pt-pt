---
title: Validação controlada da ad híbrida Azure - Azure AD
description: Aprenda a fazer uma validação controlada do Ad híbrido Azure antes de permitir que em toda a organização tudo de uma vez
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38754b9e349e27afcff58dac27a616e3e4fb5319
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96860937"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>Validação controlada de associação do Azure AD híbrido

Quando todos os pré-requisitos estiverem em vigor, os dispositivos Windows registar-se-ão automaticamente como dispositivos no seu inquilino AZure AD. O estado destas identidades do dispositivo em Azure AD é referido como a ad Azure híbrido. Mais informações sobre os conceitos abrangidos por este artigo podem ser encontradas nos artigos [Introdução à gestão de dispositivos no Azure Ative Directory](overview.md) e [Planeie a sua azure ative directy híbrida aderir à implementação.](hybrid-azuread-join-plan.md)

As organizações podem querer fazer uma validação controlada da ad híbrida Azure antes de permitir em toda a sua organização de uma só vez. Este artigo explicará como realizar uma validação controlada da ad AD híbrida.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>Validação controlada do Ad híbrido Azure junta-se a dispositivos atuais do Windows

Para os dispositivos que executam o sistema operativo windows desktop, a versão suportada é a Atualização de Aniversário do Windows 10 (versão 1607) ou posterior. Como as melhores práticas, o upgrade para a versão mais recente do Windows 10.

Para fazer uma validação controlada da ad AZure híbrida nos dispositivos atuais do Windows, você precisa:

1. Limpar a entrada do Ponto de Ligação de Serviço (SCP) do Ative Directory (AD) se existir
1. Configure a definição de registo do lado do cliente para SCP nos seus computadores ligados ao domínio utilizando um Objeto de Política de Grupo (GPO)
1. Se estiver a utilizar O FS AD, também deve configurar a definição de registo do lado do cliente para SCP no seu servidor AD FS utilizando um GPO  
1. Também pode ser necessário [personalizar opções de sincronização](../hybrid/how-to-connect-post-installation.md#additional-tasks-available-in-azure-ad-connect) no Azure AD Connect para permitir a sincronização do dispositivo. 


### <a name="clear-the-scp-from-ad"></a>Limpe o SCP da AD

Utilize o Editor de Interfaces de Serviços de Diretório Ativo (ADSI Edit) para modificar os objetos SCP em AD.

1. Lance a aplicação de desktop **ADSI Edit** a partir de uma estação de trabalho administrativa ou um controlador de domínio como Administrador Empresarial.
1. Conecte-se ao Contexto de Nomeação de **Configuração** do seu domínio.
1. Navegue para **CN=Configuração,DC=contoso,DC=com**  >  **CN=Services**  >  **CN=Configuração de Registo de Dispositivos**
1. Clique direito no objeto de folha **CN=62a0ff2e-97b9-4513-943f-0d221bd30080** e selecione **Propriedades**
   1. Selecione **palavras-chave** da janela **Do Editor de Atributos** e clique em **Editar**
   1. Selecione os valores de **azureADId** e **azureADName** (um de cada vez) e clique em **Remover**
1. Fechar **a Edição ADSI**


### <a name="configure-client-side-registry-setting-for-scp"></a>Configurar a definição de registo do lado do cliente para o SCP

Utilize o exemplo seguinte para criar um Objeto de Política de Grupo (GPO) para implementar uma definição de registo configurando uma entrada SCP no registo dos seus dispositivos.

1. Abra uma consola de Gestão de Políticas de Grupo e crie um novo Objeto de Política de Grupo no seu domínio.
   1. Forneça ao seu gpo recém-criado um nome (por exemplo, ClientSideSCP).
1. Editar o GPO e localizar o seguinte caminho: **Registo**  >  **de configurações de configuração**  >    >  **do** computador
1. Clique com o botão direito no Registo e selecione **Novo**  >  **Item de Registo**
   1. No separador **Geral,** configuure o seguinte
      1. Ação: **Atualização**
      1. Colmeia: **HKEY_LOCAL_MACHINE**
      1. Caminho chave: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Nome de valor: **TenantId**
      1. Tipo de valor: **REG_SZ**
      1. Dados de valor: O ID DO GUID ou **do Diretório** da sua instância AD Azure (Este valor pode ser encontrado no **portal Azure**  >  **Ative Directory**  >  **Properties**  >  **ID**)
   1. Clique **em OK**
1. Clique com o botão direito no Registo e selecione **Novo**  >  **Item de Registo**
   1. No separador **Geral,** configuure o seguinte
      1. Ação: **Atualização**
      1. Colmeia: **HKEY_LOCAL_MACHINE**
      1. Caminho chave: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Nome de valor: **Nome do Inquilino**
      1. Tipo de valor: **REG_SZ**
      1. Dados de valor: O seu **nome de domínio** verificado se estiver a utilizar ambientes federados, como AD FS. O **seu nome de domínio** verificado ou o nome de domínio onmicrosoft.com, por exemplo, se estiver a utilizar um ambiente `contoso.onmicrosoft.com` gerido
   1. Clique **em OK**
1. Feche o editor para o recém-criado GPO
1. Ligue o GPO recém-criado ao or desejado contendo computadores ligados ao domínio que pertencem à sua população de implantação controlada

### <a name="configure-ad-fs-settings"></a>Configurar configurações AD FS

Se estiver a utilizar o AD FS, primeiro tem de configurar o SCP do lado do cliente utilizando as instruções acima mencionadas, ligando o GPO aos seus servidores AD FS. O objeto SCP define a fonte de autoridade para objetos do dispositivo. Pode ser no local ou Azure AD. Quando o SCP do lado do cliente está configurado para AD FS, a fonte de objetos do dispositivo é estabelecida como Azure AD.

> [!NOTE]
> Se não conseguir configurar o SCP do lado do cliente nos seus servidores AD FS, a fonte para identidades do dispositivo seria considerada como no local. A ADFS começará então a eliminar objetos do dispositivo do diretório no local após o período estipulado definido no atributo "MaximumInactiveDays" do dispositivo ADFS. Os objetos de registo do dispositivo ADFS podem ser encontrados utilizando o [cmdlet get-AdfsDeviceRegistration](/powershell/module/adfs/get-adfsdeviceregistration).

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>Validação controlada do AD híbrido Azure junta-se a dispositivos de nível inferior do Windows

Para registar dispositivos de nível inferior do Windows, as organizações devem instalar [o Microsoft Workplace Join para computadores não Windows 10](https://www.microsoft.com/download/details.aspx?id=53554) disponíveis no Microsoft Download Center.

Pode implementar o pacote utilizando um sistema de distribuição de software como [o Microsoft Endpoint Configuration Manager](/configmgr/). O pacote suporta as opções de instalação silenciosa padrão com o parâmetro silencioso. O atual ramo do Gestor de Configuração oferece benefícios em versões anteriores, como a capacidade de rastrear as inscrições concluídas.

O instalador cria uma tarefa programada no sistema que funciona no contexto do utilizador. A tarefa é ativada quando o utilizador entra no Windows. A tarefa junta-se silenciosamente ao dispositivo com a Azure AD com as credenciais do utilizador depois de autenticar com a Azure AD.

Para controlar o registo do dispositivo, deverá implantar o pacote do Instalador do Windows para o seu grupo selecionado de dispositivos de nível inferior do Windows.

> [!NOTE]
> Se um SCP não estiver configurado em AD, então deve seguir a mesma abordagem descrita para configurar a [definição de registo do lado do cliente para SCP](#configure-client-side-registry-setting-for-scp)) nos seus computadores ligados ao domínio utilizando um Objeto de Política de Grupo (GPO).


Depois de verificar se tudo funciona como esperado, pode registar automaticamente o resto dos dispositivos atuais e de nível inferior do Windows com AZure AD [configurando o SCP utilizando o Azure AD Connect](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join).

## <a name="next-steps"></a>Passos seguintes

[Planeie o seu Azure Ative Directory híbrido junte-se à implementação](hybrid-azuread-join-plan.md)
