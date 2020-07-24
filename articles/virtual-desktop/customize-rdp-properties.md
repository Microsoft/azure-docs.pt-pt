---
title: Personalize propriedades RDP com PowerShell Spring 2020 - Azure
description: Como personalizar propriedades RDP para windows Virtual Desktop com cmdlets PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 07/20/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5fb992edea38c3dff9beffab1f131d3e7a6fdbf6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87087225"
---
# <a name="customize-remote-desktop-protocol-rdp-properties-for-a-host-pool"></a>Personalize propriedades do Protocolo de Ambiente de Trabalho Remoto (RDP) para uma piscina de anfitriões

>[!IMPORTANT]
>Este conteúdo aplica-se à atualização primavera 2020 com objetos de desktop virtual do Windows Manager do Azure. Se estiver a utilizar o desbloqueio virtual do Windows Desktop Fall 2019 sem objetos Azure Resource Manager, consulte [este artigo](./virtual-desktop-fall-2019/customize-rdp-properties-2019.md).
>
> A atualização Virtual Desktop Spring 2020 do Windows está atualmente em pré-visualização pública. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos a sua utilização para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A personalização das propriedades do Protocolo remoto de Ambiente de Trabalho (RDP) de uma piscina anfitriã, como experiência multi-monitor e reorientação de áudio, permite-lhe proporcionar uma experiência ideal para os seus utilizadores com base nas suas necessidades. Pode personalizar propriedades RDP no Windows Virtual Desktop utilizando o portal Azure ou utilizando o parâmetro *-CustomRdpProperty* no cmdlet **Update-AzWvdHostPool.**

Consulte [as definições de ficheiros RDP suportados](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) para obter uma lista completa de propriedades suportadas e os seus valores predefinidos.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, siga as instruções em [Configurar o módulo Virtual Desktop PowerShell](powershell-module.md) para configurar o módulo PowerShell e iniciar o súm na Azure.

## <a name="configure-rdp-properties-in-the-azure-portal"></a>Configure propriedades RDP no portal Azure

Para configurar propriedades RDP no portal Azure:

1. Inscreva-se no Azure at <https://portal.azure.com> .
2. Introduza **o ambiente de trabalho virtual do windows** na barra de pesquisa.
3. Em Serviços, selecione **Windows Virtual Desktop**.
4. Na página de Ambiente de Trabalho Virtual do Windows, selecione **as piscinas de anfitriões** no menu do lado esquerdo do ecrã.
5. Selecione **o nome da piscina de anfitrião** que pretende atualizar.
6. Selecione **Propriedades** no menu no lado esquerdo do ecrã.
7. No separador **Propriedades,** aceda às **definições de RDP** para começar a editar as propriedades RDP. As propriedades devem estar num formato separado de pontos e vírgulas como os exemplos powerShell.
8. Quando terminar, **selecione Guardar** para guardar as suas alterações.

As próximas secções dir-lhe-ão como editar as propriedades RDP personalizadas manualmente no PowerShell.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Adicione ou edite uma única propriedade RDP personalizada

Para adicionar ou editar uma única propriedade RDP personalizada, executar o seguinte cmdlet PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty <property>
```

Para verificar se o cmdlet acabou de executar a propriedade, execute este cmdlet:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

Por exemplo, se estivesse a verificar a propriedade "audiocapturemode" numa piscina de anfitriões chamada 0301HP, entraria neste cmdlet:

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;
```

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Adicione ou edite várias propriedades RDP personalizadas

Para adicionar ou editar várias propriedades RDP personalizadas, executar os seguintes cmdlets PowerShell, fornecendo as propriedades RDP personalizadas como uma cadeia separada de semicolon:

```powershell
$properties="<property1>;<property2>;<property3>"
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty $properties
```

Pode verificar se a propriedade RDP foi adicionada executando o seguinte cmdlet:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

Com base no nosso exemplo anterior do cmdlet, se configurar várias propriedades RDP na piscina de anfitriões 0301HP, o seu cmdlet seria assim:

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;audiomode:i:0;
```

## <a name="reset-all-custom-rdp-properties"></a>Redefinir todas as propriedades RDP personalizadas

Pode redefinir as propriedades RDP personalizadas individuais para os seus valores predefinidos seguindo as instruções em [Adicionar ou editar uma única propriedade DDP personalizada,](#add-or-edit-a-single-custom-rdp-property)ou pode redefinir todas as propriedades RDP personalizadas para uma piscina de anfitrião executando o seguinte cmdlet PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty ""
```

Para se certificar de que removeu com sucesso a definição, introduza este cmdlet:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <CustomRDPpropertystring>
```

## <a name="next-steps"></a>Passos seguintes

Agora que personalizou as propriedades RDP para uma determinada piscina de anfitriões, pode iniciar sessão num cliente virtual do Windows desktop para testá-las como parte de uma sessão de utilizador. Estes próximos guias de como ligar-se a uma sessão utilizando o cliente à sua escolha:

- [Ligar ao cliente de Ambiente de Trabalho do Windows](connect-windows-7-10.md)
- [Ligar com o cliente web](connect-web.md)
- [Ligar ao cliente Android](connect-android.md)
- [Ligar ao cliente de macOS](connect-macos.md)
- [Ligar ao cliente de iOS](connect-ios.md)
