---
title: Manual de Início Rápido do Azure - Configurar uma VM com DSC | Microsoft Docs
description: Configurar uma Pilha LAMP numa Máquina Virtual Linux com a Configuração do Estado Pretendido
services: automation
ms.subservice: dsc
keywords: dsc, configuração, automatização
ms.date: 11/06/2018
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 1a146ab7c05d200b71a33a72fa6362c3cf62629a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457523"
---
# <a name="configure-a-virtual-machine-with-desired-state-configuration"></a>Configure uma máquina virtual com configuração de estado desejada

Ao ativar a Configuração do Estado da Automatização Azure, pode gerir e monitorizar as configurações dos seus servidores Windows e Linux utilizando a Configuração do Estado Desejado (DSC). As configurações que derivam de uma configuração desejada podem ser identificadas ou corrigidas automaticamente. Este guia rápido acompanha-o ao longo do carregamento de uma VM Linux e implementação de uma pilha LAMP com DSC.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido, necessita de:

* Uma subscrição do Azure. Se não tiver uma subscrição Azure, [crie uma conta gratuita.](https://azure.microsoft.com/free/)
* Uma conta de Automatização do Azure. Para obter instruções sobre como criar uma conta Run As de Automatização do Azure, veja [Conta Run As do Azure](automation-sec-configure-azure-runas-account.md).
* Uma VM do Azure Resource Manager (não Clássica) com Red Hat Enterprise Linux, CentOS ou Oracle Linux. Para obter instruções sobre como criar uma VM, veja [Criar a sua primeira máquina virtual do Linux no portal do Azure](../virtual-machines/linux/quick-create-portal.md)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Inscreva-se no https://portal.azure.comAzure em .

## <a name="onboard-a-virtual-machine"></a>Carregar uma máquina virtual

Existem muitos métodos diferentes para embarcar numa máquina e ativar o DSC. Este guia rápido abrange a inclusão através de uma conta de Automatização. Pode aprender mais sobre diferentes métodos para embarcar nas suas máquinas para configuração do Estado, lendo o artigo [de onboarding.](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

1. No painel esquerdo do portal do Azure, selecione **Contas de Automatização**. Se não estiver visível no painel esquerdo, clique em **Todos os serviços** e procure na vista resultante.
1. Na lista, selecione uma conta de Automatização.
1. No painel esquerdo da conta de Automatização, selecione **Configuração de estado (DSC)**.
2. Clique em **Adicionar** para abrir a página de seleção de VM.
3. Encontre a máquina virtual para a qual ativar o DSC. Pode utilizar as opções de campo de pesquisa e de filtro para encontrar uma máquina virtual específica.
4. Clique na máquina virtual e, em seguida, clique em **Connect**
5. Selecione as definições de DSC adequadas para a máquina virtual. Se já preparou uma configuração, `Node Configuration Name`pode especificá-la como . Pode definir o [modo de configuração](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaConfig) para controlar o comportamento de configuração da máquina.
6. Clique em **OK**. Enquanto a extensão DSC é implantada na `Connecting`máquina virtual, o estado aparece como .

![Inclusão de uma VM do Azure no DSC](./media/automation-quickstart-dsc-configuration/dsc-onboard-azure-vm.png)

## <a name="import-modules"></a>Importar módulos

Os módulos contêm recursos DSC e muitos podem ser encontrados na [Galeria PowerShell.](https://www.powershellgallery.com) Quaisquer recursos utilizados nas suas configurações devem ser importados para a conta Automation antes de serem compilados. Para este tutorial, é preciso o módulo denominado **nx**.

1. No painel esquerdo da conta Automation, selecione **Modules Gallery** em **Recursos Partilhados.**
1. Procure o módulo para importar digitando `nx`parte do seu nome: .
1. Clique no módulo para importar.
1. Clique **em Importar**.

![Importar um Módulo de DSC](./media/automation-quickstart-dsc-configuration/dsc-import-module-nx.png)

## <a name="import-the-configuration"></a>Importar a configuração

Este guia rápido utiliza uma configuração de DSC que configura o Apache HTTP Server, o MySQL e o PHP na máquina. Ver [configurações DSC](https://docs.microsoft.com/powershell/scripting/dsc/configurations/configurations).

Num editor de texto, escreva o seguinte e guarde-o localmente como **AMPServer.ps1**.

```powershell-interactive
configuration LAMPServer {
   Import-DSCResource -module "nx"

   Node localhost {

        $requiredPackages = @("httpd","mod_ssl","php","php-mysql","mariadb","mariadb-server")
        $enabledServices = @("httpd","mariadb")

        #Ensure packages are installed
        ForEach ($package in $requiredPackages){
            nxPackage $Package{
                Ensure = "Present"
                Name = $Package
                PackageManager = "yum"
            }
        }

        #Ensure daemons are enabled
        ForEach ($service in $enabledServices){
            nxService $service{
                Enabled = $true
                Name = $service
                Controller = "SystemD"
                State = "running"
            }
        }
   }
}
```

Para importar a configuração:

1. No painel esquerdo da conta de Automatização, selecione **Configuração de estado (DSC)** e, em seguida, clique no separador **Configurações**.
2. Clique em **+ Adicionar**.
3. Selecione o ficheiro de configuração que guardou no passo anterior.
4. Clique em **OK**.

## <a name="compile-a-configuration"></a>Compilar uma configuração

Deve compilar uma configuração DSC para uma configuração nó (documento MOF) antes de ser atribuída a um nó. A compilação valida a configuração e permite a entrada de valores de parâmetros. Para saber mais sobre a compilação de uma configuração, consulte [configurações de compilação na Configuração do Estado](automation-dsc-compile.md).

1. No painel esquerdo da conta Automation, selecione **Configuração do Estado (DSC)** e, em seguida, clique no separador **Configurações.**
1. Selecione `LAMPServer`a configuração .
1. A partir das opções do menu, **selecione Compile** e, em seguida, clique **em Sim**.
1. Na vista de Configuração, vê-se um novo trabalho de compilação em fila. Quando a tarefa for concluída com êxito, está pronta para avançar para o passo seguinte. Se houver falhas, pode clicar no trabalho de compilação para obter mais detalhes.

## <a name="assign-a-node-configuration"></a>Atribuir uma configuração de nó

Pode atribuir uma configuração de nó compilada a um nó DSC. A atribuição aplica a configuração à máquina e monitores ou auto-corrige para qualquer deriva dessa configuração.

1. No painel esquerdo da conta Automation, selecione **Configuração do Estado (DSC)** e, em seguida, clique no separador **Nós.**
1. Selecione o nó para atribuir uma configuração.
1. Clique em **Atribuir Configuração do Nó**
1. Selecione a `LAMPServer.localhost` configuração do nó e clique **OK**. A configuração do Estado atribui agora a configuração compilada `Pending`ao nó, e o estado do nó muda para . Na próxima verificação periódica, o nó recupera a configuração, aplica-a e reporta o estado. Pode levar até 30 minutos para o nó recuperar a configuração, dependendo das definições do nó. 
1. Para forçar uma verificação de imediato, pode executar o comando seguinte localmente na máquina virtual Linux: `sudo /opt/microsoft/dsc/Scripts/PerformRequiredConfigurationChecks.py`

![Atribuir uma Configuração do Nó](./media/automation-quickstart-dsc-configuration/dsc-assign-node-configuration.png)

## <a name="view-node-status"></a>Ver estado do nó

Pode visualizar o estado de todos os nós geridos pela Configuração do Estado na sua conta Deautomação. A informação é exibida escolhendo a **Configuração do Estado (DSC)** e clicando no separador **Nós.** Pode filtrar o visor por estado, configuração do nó ou pesquisa de nomes.

![Estado do Nó DSC](./media/automation-quickstart-dsc-configuration/dsc-node-status.png)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, você abordou um Linux VM para configuração de estado, criou uma configuração para uma pilha LAMP, e implementou a configuração para o VM. Para saber como pode utilizar a Configuração do Estado da Automação Azure para permitir a implantação contínua, continue o artigo:

> [!div class="nextstepaction"]
> [Implementação contínua para uma VM com o DSC e Chocolatey](./automation-dsc-cd-chocolatey.md)

* Para saber mais sobre o PowerShell DSC, consulte a visão geral da [Configuração do Estado desejada powerShell](https://docs.microsoft.com/powershell/scripting/dsc/overview/overview).
* Para saber mais sobre a gestão da Configuração do Estado a partir da PowerShell, consulte [o Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.automation/).
* Para saber como encaminhar relatórios da DSC para registos do Monitor Azure para reporte e alerta, consulte o [Reencaminhador DSC reportando aos registos do Monitor Azure](automation-dsc-diagnostics.md).