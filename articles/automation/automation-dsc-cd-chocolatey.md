---
title: Implantação contínua de configuração do Estado da Automatização Azure com Chocolatey
description: DevOps implantação contínua usando Configuração do Estado de Automação Azure, DSC e gestor de pacotes chocolatey.  Exemplo com modelo completo do Gestor de Recursos JSON e fonte PowerShell.
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.openlocfilehash: 4445f6e9b72380b66f3282d50871b4283f7fc7fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75966744"
---
# <a name="usage-example-continuous-deployment-to-virtual-machines-using-automation-state-configuration-and-chocolatey"></a>Exemplo de utilização: Implantação contínua para máquinas virtuais utilizando configuração do Estado da Automação e Chocolatey

Num mundo de DevOps existem muitas ferramentas para ajudar com vários pontos no pipeline de Integração Contínua. A Configuração do Estado da Automação Azure é uma nova adição bem-vinda às opções que as equipas da DevOps podem empregar. Este artigo demonstra a criação de uma implementação contínua (CD) para um computador Windows. Pode facilmente estender a técnica para incluir o número de computadores Windows necessários na função (um web site, por exemplo), e a partir daí para funções adicionais também.

![Implantação contínua para VMs IaaS](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>A um nível elevado

Há um pouco acontecendo aqui, mas felizmente pode ser quebrado em dois processos principais:

- Escrever código e testá-lo, criando e publicando pacotes de instalação para versões importantes e menores do sistema.
- Criação e gestão de VMs que irão instalar e executar o código nos pacotes.  

Uma vez que ambos os processos principais estão em vigor, é um pequeno passo para atualizar automaticamente o pacote em execução em qualquer VM particular à medida que novas versões são criadas e implementadas.

## <a name="component-overview"></a>Visão geral do componente

Gestores de pacotes como [o apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) são bastante conhecidos no mundo linux, mas não tanto no mundo windows.
[Chocolatey](https://chocolatey.org/) é uma coisa, e o [blog](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) de Scott Hanselman sobre o tema é uma grande introdução. Em resumo, o Chocolatey permite-lhe instalar pacotes a partir de um repositório central de embalagens num sistema Windows utilizando a linha de comando. Pode criar e gerir o seu próprio repositório, e a Chocolatey pode instalar pacotes a partir de vários repositórios que designasse.

A Configuração do Estado Desejada (DSC)[(visão geral)](/powershell/scripting/dsc/overview/overview)é uma ferramenta PowerShell que lhe permite declarar a configuração que deseja para uma máquina. Por exemplo, pode dizer: "Quero chocolate instalado, quero o IIS instalado, quero a porta 80 aberta, quero a versão 1.0.0 do meu website instalada." O Gestor de Configuração Local DSC (LCM) implementa esta configuração. Um DSC Pull Server contém um repositório de configurações para as suas máquinas. O LCM de cada máquina faz verificaperiodicamente para ver se a sua configuração corresponde à configuração armazenada. Pode relatar o estado ou tentar realinhar a máquina com a configuração armazenada. Pode editar a configuração armazenada no servidor pull para fazer com que uma máquina ou um conjunto de máquinas entrem em alinhamento com a configuração alterada.

O Azure Automation é um serviço gerido no Microsoft Azure que permite automatizar várias tarefas usando livros de corridas, nós, credenciais, recursos e ativos, como horários e variáveis globais.
A Configuração do Estado da Automação Azure alarga esta capacidade de automatização para incluir ferramentas DSC PowerShell. Aqui está uma grande [visão geral.](automation-dsc-overview.md)

Um Recurso DSC é um módulo de código que tem capacidades específicas, tais como gestão de networking, Ative Directory ou SQL Server. O Chocolatey DSC Resource sabe como aceder a um NuGet Server (entre outros), descarregar pacotes, instalar pacotes, e assim por diante. Existem muitos outros Recursos DSC na [Galeria PowerShell.](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title)
Estes módulos estão instalados no seu Servidor de Pull De Configuração do Estado de Automação Azure (por si) para que possam ser utilizados pelas suas configurações.

Os modelos do Gestor de Recursos fornecem uma forma declarativa de gerar a sua infraestrutura - coisas como redes, subredes, segurança de rede e encaminhamento, equilibradores de carga, NICs, VMs, e assim por diante. Aqui está um [artigo](../azure-resource-manager/management/deployment-models.md) que compara o modelo de implementação do Gestor de Recursos (declarativo) com o modelo de implementação de serviços azure (ASM ou clássico) (imperativo), e discute os principais fornecedores de recursos, computação, armazenamento e rede.

Uma característica chave de um modelo de Gestor de Recursos é a sua capacidade de instalar uma extensão VM no VM tal como é provisionado. Uma extensão VM tem capacidades específicas, tais como executar um script personalizado, instalar software antivírus ou executar um script de configuração DSC. Existem muitos outros tipos de extensões VM.

## <a name="quick-trip-around-the-diagram"></a>Viagem rápida ao redor do diagrama

Começando por cima, escreve o seu código, constrói e testa e, em seguida, cria um pacote de instalação.
O chocolate pode lidar com vários tipos de pacotes de instalação, tais como MSI, MSU, ZIP. E tens todo o poder da PowerShell para fazer a instalação real se as capacidades nativas do Chocolateys não estiverem à altura. Coloque o pacote em algum lugar acessível - um repositório de pacotes. Este exemplo de utilização usa uma pasta pública numa conta de armazenamento de blob Azure, mas pode estar em qualquer lugar. O Chocolatey funciona de forma nativa com servidores NuGet e alguns outros para a gestão de metadados de pacotes. [Este artigo](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) descreve as opções. Este exemplo de utilização utiliza o NuGet. Um Nuspec é metadados sobre os seus pacotes. Os Nuspec's são "compilados" em NuPkg's e armazenados num servidor NuGet. Quando a sua configuração solicita um pacote pelo nome e faz referência a um servidor NuGet, o Recurso Chocolatey DSC (agora no VM) agarra a embalagem e instala-a para si. Também pode solicitar uma versão específica de um pacote.

Na parte inferior esquerda da imagem, há um modelo de Gestor de Recursos Azure. Neste exemplo de utilização, a extensão VM regista o VM com o Servidor de Pull Pull de Configuração do Estado de Automação Azure (isto é, um servidor de puxar) como nó. A configuração é armazenada no servidor pull.
Na verdade, é armazenado duas vezes: uma como texto simples e uma vez compilado como um ficheiro MOF (para aqueles que sabem sobre tais coisas.) No portal, o MOF é uma "configuração nó" (em oposição a simplesmente "configuração"). É o artefacto que está associado a um Nó para que o nó conheça a sua configuração. Os detalhes abaixo mostram como atribuir a configuração do nó ao nó.

Presumivelmente já estás a fazer a parte de cima, ou a maior parte. Criar o nuspec, compilar e armazenar num servidor NuGet é uma coisa pequena. E já estás a gerir VMs. Dar o próximo passo para a implementação contínua requer a configuração do servidor de puxar (uma vez), registar os seus nós com ele (uma vez) e criar e armazenar a configuração lá (inicialmente). Em seguida, à medida que as embalagens são atualizadas e implantadas para o repositório, refresque a Configuração de Configuração e Nó no servidor de puxar (repita conforme necessário).

Se não está a começar com um modelo de Gestor de Recursos, também está tudo bem. Existem cmdlets PowerShell projetados para ajudá-lo a registar os seus VMs com o servidor pull e todo o resto. Para mais detalhes, consulte este artigo: [Máquinas de embarque para gestão pela Configuração do Estado da Automação Azure](automation-dsc-onboarding.md).

## <a name="step-1-setting-up-the-pull-server-and-automation-account"></a>Passo 1: Configurar o servidor de puxar e a conta de automação

Numa linha de`Connect-AzureRmAccount`comando autenticada () PowerShell: (pode demorar alguns minutos enquanto o servidor de puxar está configurado)

```azurepowershell-interactive
New-AzureRmResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzureRmAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

Você pode colocar a sua conta de automação em qualquer uma das seguintes regiões (aka localização): Leste DOS 2, Sul dos EUA, EUA Gov Virginia, Europa Ocidental, Sudeste Asiático, Japão Leste, Índia Central e Austrália Sudeste, Canadá Central, Norte da Europa.

## <a name="step-2-vm-extension-tweaks-to-the-resource-manager-template"></a>Passo 2: Ajustes de extensão VM para o modelo de Gestor de Recursos

Detalhes para o registo VM (utilizando a extensão PowerShell DSC VM) fornecidos neste [modelo De Arranque Rápido Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver).
Este passo regista o seu novo VM com o servidor de puxar na lista de nós de configuração do Estado. Parte deste registo é especificar a configuração do nó a aplicar ao nó. Esta configuração do nó ainda não tem de existir no servidor pull, por isso é OK que o Passo 4 seja onde isto é feito pela primeira vez. Mas aqui no passo 2 precisa de ter decidido o nome do nó e o nome da configuração. Neste exemplo de utilização, o nó é 'isvbox' e a configuração é 'ISVBoxConfig'. Assim, o nome de configuração do nó (a especificar em DeploymentTemplate.json) é 'ISVBoxConfig.isvbox'.

## <a name="step-3-adding-required-dsc-resources-to-the-pull-server"></a>Passo 3: Adicionar os recursos dSC necessários ao servidor de puxar

A PowerShell Gallery está instrumentada para instalar recursos DSC na sua conta Azure Automation.
Navegue para o recurso que deseja e clique no botão "Implementar para a Automatização Azure".

![Exemplo da Galeria PowerShell](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Outra técnica recentemente adicionada ao Portal Azure permite-lhe puxar novos módulos ou atualizar os módulos existentes. Clique no recurso da Conta de Automação, no azulejo Assets e, finalmente, no azulejo dos Módulos. O ícone da Galeria De Navegação permite-lhe ver a lista de módulos na galeria, aprofundar detalhes e, em última análise, importar para a sua Conta de Automação. Esta é uma ótima maneira de manter os seus módulos atualizados de vez em quando. E, a funcionalidade de importação verifica dependências com outros módulos para garantir que nada se dessincroniza.

Ou há a abordagem manual. A estrutura de pasta de um Módulo de Integração PowerShell para um computador Windows é um pouco diferente da estrutura de pasta esperada pela Automação Azure.
Isto requer um pequeno ajuste da sua parte. Mas não é difícil, e é feito apenas uma vez por recurso (a menos que queira atualizá-lo no futuro.) Para obter mais informações sobre a autoria de Módulos de Integração PowerShell, consulte este artigo: [Autorde Módulos de Integração para Automação Azure](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)

- Instale o módulo que necessita na sua estação de trabalho, da seguinte forma:
  - Instalar [o Windows Management Framework, v5](https://aka.ms/wmf5latest) (não necessário para o Windows 10)
  - `Install-Module –Name MODULE-NAME`< — agarra o módulo da Galeria PowerShell
- Copiar a pasta `c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME` do módulo de uma pasta temporária
- Eliminar amostras e documentação da pasta principal
- Feche a pasta principal, nomeando o ficheiro ZIP exatamente o mesmo que a pasta 
- Coloque o ficheiro ZIP numa localização HTTP acessível, como armazenamento de blob numa Conta de Armazenamento Azure.
- Executa esta PowerShell:

  ```powershell
  New-AzureRmAutomationModule `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Name MODULE-NAME –ContentLink 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
  ```

O exemplo incluído executa estes passos para cChoco e xNetworking. Consulte as [Notas](#notes) para manuseamento especial para cChoco.

## <a name="step-4-adding-the-node-configuration-to-the-pull-server"></a>Passo 4: Adicionar a configuração do nó ao servidor de puxar

Não há nada de especial na primeira vez que importas a tua configuração para o servidor de puxar e compilar. Todas as importações/compilações subsequentes da mesma configuração são exatamente iguais. Cada vez que atualiza o seu pacote e precisa de o empurrar para a produção, faz este passo depois de garantir que o ficheiro de configuração está correto – incluindo a nova versão do seu pacote. Aqui está o ficheiro de configuração e PowerShell:

ISVBoxConfig.ps1:

```powershell
Configuration ISVBoxConfig
{
    Import-DscResource -ModuleName cChoco
    Import-DscResource -ModuleName xNetworking

    Node 'isvbox' {

        cChocoInstaller installChoco
        {
            InstallDir = 'C:\choco'
        }

        WindowsFeature installIIS
        {
            Ensure = 'Present'
            Name   = 'Web-Server'
        }

        xFirewall WebFirewallRule
        {
            Direction    = 'Inbound'
            Name         = 'Web-Server-TCP-In'
            DisplayName  = 'Web Server (TCP-In)'
            Description  = 'IIS allow incoming web site traffic.'
            Enabled       = 'True'
            Action       = 'Allow'
            Protocol     = 'TCP'
            LocalPort    = '80'
            Ensure       = 'Present'
        }

        cChocoPackageInstaller trivialWeb
        {
            Name      = 'trivialweb'
            Version   = '1.0.0'
            Source    = 'MY-NUGET-V2-SERVER-ADDRESS'
            DependsOn = '[cChocoInstaller]installChoco','[WindowsFeature]installIIS'
        }
    }
}
```

Nova ConfiguraçãoScript.ps1:

```powershell
Import-AzureRmAutomationDscConfiguration `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 `
    -Published –Force

$jobData = Start-AzureRmAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -ConfigurationName ISVBoxConfig

$compilationJobId = $jobData.Id

Get-AzureRmAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Id $compilationJobId
```

Estes passos resultam numa nova configuração do nó chamada "ISVBoxConfig.isvbox" colocada no servidor pull. O nome de configuração do nó é construído como "configuraçãoName.nodeName".

## <a name="step-5-creating-and-maintaining-package-metadata"></a>Passo 5: Criação e manutenção de metadados de pacotes

Para cada pacote que coloca no repositório do pacote, precisa de uma nuspecção que o descreva.
Esse nuspecção deve ser compilado e armazenado no seu servidor NuGet. Este processo é descrito [aqui.](https://docs.nuget.org/create/creating-and-publishing-a-package) Pode usar MyGet.org como servidor NuGet. Vendem este serviço, mas têm um SKU de entrada que é gratuito. No NuGet.org encontrará instruções sobre a instalação do seu próprio servidor NuGet para os seus pacotes privados.

## <a name="step-6-tying-it-all-together"></a>Passo 6: Amarrar tudo junto

Cada vez que uma versão passa qA e é aprovada para implementação, o pacote é criado, nuspece e nupkg atualizado e implantado para o servidor NuGet. Além disso, a configuração (passo 4 acima) deve ser atualizada para concordar com o número da nova versão. Deve ser enviado para o servidor de puxar e compilado.
A partir daí, cabe aos VMs que dependem dessa configuração puxar a atualização e instalá-la. Cada uma destas atualizações é simples - apenas uma linha ou duas de PowerShell. No caso dos DevOps Azure, alguns deles são encapsulado em tarefas de construção que podem ser acorrentadas numa construção. Este [artigo](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) fornece mais detalhes. Este [repo GitHub](https://github.com/Microsoft/vso-agent-tasks) detalha as várias tarefas de construção disponíveis.

## <a name="notes"></a>Notas

Este exemplo de utilização começa com um VM a partir de uma imagem genérica do Windows Server 2012 R2 da galeria Azure. Pode começar a partir de qualquer imagem armazenada e depois ajustar a partir daí com a configuração DSC.
No entanto, mudar a configuração que é cozida numa imagem é muito mais difícil do que atualizar dinamicamente a configuração usando DSC.

Não é preciso usar um modelo de Gestor de Recursos e a extensão VM para utilizar esta técnica com os seus VMs. E os teus VMs não têm de estar no Azure para estarem sob gestão de CD. Tudo o que é necessário é que o Chocolatey seja instalado e o LCM configurado no VM para saber onde está o servidor de puxar.

Claro que, quando atualizas um pacote de um VM que está em produção, precisas de tirar esse VM da rotação enquanto a atualização está instalada. A forma como o fazes varia muito. Por exemplo, com um VM atrás de um Balancer de Carga Azure, pode adicionar uma Sonda Personalizada. Ao atualizar o VM, mande a sonda devolver um 400. O ajuste necessário para causar esta alteração pode estar dentro da sua configuração, assim como o ajuste para alternar para devolver um 200 assim que a atualização estiver completa.

Fonte completa para este exemplo de uso está [neste projeto do Estúdio Visual](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) no GitHub.

## <a name="related-articles"></a>Artigos Relacionados
* [Visão geral do DSC da Automação Azure](automation-dsc-overview.md)
* [Cmdlets DSC da Automação Azure](https://docs.microsoft.com/powershell/module/azurerm.automation#automation)
* [Máquinas de embarque para gestão pela Azure Automation DSC](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral, consulte a Configuração do Estado da [Automação Azure](automation-dsc-overview.md)
- Para começar, veja Começar com a Configuração do Estado da [Automação Azure](automation-dsc-getting-started.md)
- Para aprender sobre a compilação de configurações de DSC para que possa atribuí-las a nós-alvo, consulte [Configurações de Compilação na Configuração do Estado da Automação Azure](automation-dsc-compile.md)
- Para referência de cmdlet PowerShell, consulte [Os cmdlets](/powershell/module/azurerm.automation/#automation) de configuração do Estado da Automatização Do Azure
- Para obter informações sobre preços, consulte os preços de configuração do Estado da [Automação Do Azure](https://azure.microsoft.com/pricing/details/automation/)
- Para ver um exemplo de utilização da Configuração do Estado da Automação Azure num pipeline de implantação contínua, consulte a implantação contínua utilizando a configuração do Estado da [Automação Azure e](automation-dsc-cd-chocolatey.md) o Chocolatey
