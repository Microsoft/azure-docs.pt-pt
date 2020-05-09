---
title: Personalize propriedades RDP com PowerShell - Azure
description: Como personalizar propriedades RDP para Windows Virtual Desktop com cmdlets PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 66b76fcdd9729b2a92ea2d561c740dbe148e0bbe
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611556"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Personalize propriedades do Protocolo de Ambiente de Trabalho Remoto para uma piscina de anfitriões

>[!IMPORTANT]
>Este conteúdo aplica-se à atualização da primavera de 2020 com os objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure Windows. Se estiver a utilizar o lançamento do Windows Virtual Desktop Fall 2019 sem objetos do Gestor de Recursos Azure, consulte [este artigo](./virtual-desktop-fall-2019/customize-rdp-properties-2019.md).
>
> A atualização Do Windows Virtual Desktop Spring 2020 encontra-se atualmente em pré-visualização pública. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos usá-la para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. 
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

Personalizar as propriedades do Protocolo de Ambiente de Trabalho Remoto (RDP) de um pool anfitrião, como experiência de multimonitor e reorientação de áudio, permite-lhe oferecer uma experiência ideal para os seus utilizadores com base nas suas necessidades. Pode personalizar propriedades RDP no Windows Virtual Desktop utilizando o portal Azure ou utilizando o parâmetro *-CustomRdpProperty* no cmdlet **Update-AzWvdHostPool.**

Consulte as definições de [ficheiroRD Suportadas](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) para obter uma lista completa de propriedades suportadas e os seus valores predefinidos.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, siga as instruções no [Conjunto do módulo PowerShell virtual do Windows](powershell-module.md) para configurar o módulo PowerShell e iniciar o seu início no Azure.

## <a name="default-rdp-properties"></a>Propriedades de RDP padrão

Por predefinição, os ficheiros RDP publicados contêm as seguintes propriedades:

|Propriedades de RDP | Computadores de Secretária | Aplicações Remotas |
|---|---| --- |
| Modo multi-monitor | Ativado | N/D |
| Redirecionamentos de acionamento ativados | Unidades, clipboard, impressoras, portas COM, dispositivos USB e smartcards| Unidades, clipboard e impressoras |
| Modo de áudio remoto | Jogar localmente | Jogar localmente |

Quaisquer propriedades personalizadas que defina para a piscina anfitriã anularão estes incumprimentos.

## <a name="configure-rdp-properties-in-the-azure-portal"></a>Configure propriedades de RDP no portal Azure

Para configurar propriedades de PDR no portal Azure:

1. Inscreva-se no <https://portal.azure.com>Azure em .
2. Introduza **o ambiente de trabalho virtual das janelas** na barra de pesquisa.
3. Em Serviços, selecione **Windows Virtual Desktop**.
4. Na página do Windows Virtual Desktop, selecione **as piscinas** de anfitriões no menu do lado esquerdo do ecrã.
5. Selecione **o nome da piscina** anfitriã que pretende atualizar.
6. Selecione **Propriedades** no menu no lado esquerdo do ecrã.
7. Selecione **definições de RDP** para começar a editar as propriedades RDP.
8. Quando terminar, selecione **Guardar** para guardar as suas alterações.

Se houver uma definição que pretende editar que não vê no menu de definições de RDP, terá de editá-la manualmente executando cmdlets no PowerShell. As próximas secções dirão como editar propriedades de RDP personalizadas manualmente no PowerShell.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Adicione ou edite uma única propriedade personalizada rdp

Para adicionar ou editar uma única propriedade personalizada de RDP, executar o seguinte cmdlet PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty <property>
```

Para verificar se o cmdlet acabou de executar atualizou a propriedade, execute este cmdlet:

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

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Adicionar ou editar várias propriedades personalizadas de RDP

Para adicionar ou editar várias propriedades personalizadas de RDP, execute os seguintes cmdlets PowerShell, fornecendo as propriedades de RDP personalizadas como uma cadeia separada pelo ponto-ponto:

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

Com base no nosso exemplo anterior da CMDLET, se configurar várias propriedades RDP na piscina anfitriã de 0301HP, o seu cmdlet seria assim:

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty 

Name              : 0301HP 
CustomRdpProperty : audiocapturemode:i:1;audiomode:i:0;
```

## <a name="reset-all-custom-rdp-properties"></a>Redefinir todas as propriedades personalizadas de RDP

Pode redefinir propriedades de RDP personalizadas individuais para os seus valores padrão seguindo as instruções em [Adicionar ou editar uma única propriedade RDP personalizada,](#add-or-edit-a-single-custom-rdp-property)ou pode redefinir todas as propriedades de RDP personalizadas para uma piscina hospedeira executando o seguinte cmdlet PowerShell:

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

Agora que personalizou as propriedades rdp para uma determinada piscina de anfitriões, pode iniciar sessão com um cliente do Windows Virtual Desktop para testá-las como parte de uma sessão de utilizador. Estes próximos guias de como-a-adirão como se conectar a uma sessão usando o cliente da sua escolha:

- [Ligar ao cliente de Ambiente de Trabalho do Windows](connect-windows-7-and-10.md)
- [Ligar com o cliente web](connect-web.md)
- [Ligar ao cliente Android](connect-android.md)
- [Ligar ao cliente de macOS](connect-macos.md)
- [Ligar ao cliente de iOS](connect-ios.md)