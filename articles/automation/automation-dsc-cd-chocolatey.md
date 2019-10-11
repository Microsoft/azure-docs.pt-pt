---
title: Implantação contínua da configuração de estado da automação do Azure com Chocolatey
description: DevOps implantação contínua usando a configuração de estado da automação do Azure, DSC e o Gerenciador de pacotes de Chocolatey.  Exemplo com o modelo do Gerenciador de recursos JSON completo e a origem do PowerShell.
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 08/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f4512b79873d7f770b32a452a02c53bc5575bdac
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243593"
---
# <a name="usage-example-continuous-deployment-to-virtual-machines-using-automation-state-configuration-and-chocolatey"></a>Exemplo de uso: implantação contínua em máquinas virtuais usando configuração de estado de automação e Chocolatey

Em um mundo DevOps, há muitas ferramentas para ajudá-lo com vários pontos no pipeline de integração contínua. A configuração de estado da automação do Azure é uma nova adição de boas-vindas às opções que as equipes de DevOps podem empregar. Este artigo demonstra como configurar a implantação contínua (CD) para um computador Windows. Você pode estender facilmente a técnica para incluir tantos computadores com Windows quantos forem necessários na função (um site, por exemplo) e, a partir daí, também para funções adicionais.

![Implantação contínua para VMs IaaS](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>Em um alto nível

Há muito pouco acontecendo aqui, mas felizmente ele pode ser dividido em dois processos principais:

- Escrever código e testá-lo, criando e publicando pacotes de instalação para versões principais e secundárias do sistema.
- Criar e gerenciar VMs que irão instalar e executar o código nos pacotes.  

Uma vez que ambos os processos principais estejam em vigor, é uma etapa breve atualizar automaticamente o pacote em execução em qualquer VM específica à medida que novas versões são criadas e implantadas.

## <a name="component-overview"></a>Visão geral do componente

Os gerenciadores de pacotes como [apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) são bem conhecidos no mundo Linux, mas não muito no mundo do Windows.
O [Chocolatey](https://chocolatey.org/) é tal coisa, e o [blog](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) de Scott Hanselman sobre o assunto é uma ótima introdução. Resumindo, Chocolatey permite que você instale pacotes de um repositório central de pacotes em um sistema Windows usando a linha de comando. Você pode criar e gerenciar seu próprio repositório e o Chocolatey pode instalar pacotes de qualquer número de repositórios que você designar.

A DSC (configuração de estado desejado) ([visão geral](/powershell/scripting/dsc/overview/overview)) é uma ferramenta do PowerShell que permite declarar a configuração desejada para um computador. Por exemplo, você pode dizer, "quero que o Chocolatey esteja instalado, desejo que o IIS seja instalado, quero que a porta 80 seja aberta, desejo que a versão 1.0.0 do meu site esteja instalada". O Configuration Manager local de DSC (LCM) implementa essa configuração. Um servidor de pull de DSC mantém um repositório de configurações para seus computadores. O LCM em cada computador faz check-in periodicamente para ver se sua configuração corresponde à configuração armazenada. Ele pode relatar o status ou tentar colocar o computador novamente em alinhamento com a configuração armazenada. Você pode editar a configuração armazenada no servidor de pull para fazer com que um computador ou conjunto de computadores fique alinhado com a configuração alterada.

A automação do Azure é um serviço gerenciado no Microsoft Azure que permite automatizar várias tarefas usando runbooks, nós, credenciais, recursos e ativos, como agendas e variáveis globais.
A configuração de estado da automação do Azure estende esse recurso de automação para incluir as ferramentas de DSC do PowerShell. Aqui está uma ótima [visão geral](automation-dsc-overview.md).

Um recurso de DSC é um módulo de código que tem recursos específicos, como o gerenciamento de rede, Active Directory ou SQL Server. O recurso DSC de Chocolatey sabe como acessar um servidor NuGet (entre outros), baixar pacotes, instalar pacotes e assim por diante. Há muitos outros recursos de DSC no [Galeria do PowerShell](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title).
Esses módulos são instalados em seu servidor de pull de configuração de estado de automação do Azure (por você) para que possam ser usados por suas configurações.

Os modelos do Resource Manager fornecem uma maneira declarativa de gerar sua infraestrutura – coisas como redes, sub-redes, segurança de rede e roteamento, balanceadores de carga, NICs, VMs e assim por diante. Aqui está um [artigo](../azure-resource-manager/resource-manager-deployment-model.md) que compara o modelo de implantação do Gerenciador de recursos (declarativo) com o modelo de implantação (ASM ou clássico) do gerenciamento de serviços do Azure e discute os principais provedores de recursos, computação, armazenamento e rede.

Um recurso importante de um modelo do Resource Manager é sua capacidade de instalar uma extensão de VM na VM à medida que ela é provisionada. Uma extensão de VM tem recursos específicos, como a execução de um script personalizado, a instalação de software antivírus ou a execução de um script de configuração DSC. Há muitos outros tipos de extensões de VM.

## <a name="quick-trip-around-the-diagram"></a>Viagem rápida em todo o diagrama

Começando na parte superior, você escreve seu código, cria e testa e, em seguida, cria um pacote de instalação.
O Chocolatey pode lidar com vários tipos de pacotes de instalação, como MSI, MSU, ZIP. E você tem todo o poder do PowerShell para fazer a instalação real se as funcionalidades nativas de chocolate. Coloque o pacote em algum lugar acessível – um repositório de pacotes. Este exemplo de uso usa uma pasta pública em uma conta de armazenamento de BLOBs do Azure, mas pode ser em qualquer lugar. O Chocolatey funciona nativamente com servidores NuGet e alguns outros para o gerenciamento de metadados do pacote. [Este artigo](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) descreve as opções. Este exemplo de uso usa o NuGet. Um Nuspec são metadados sobre seus pacotes. Os Nuspec são "compilados" no NuPkg e armazenados em um servidor NuGet. Quando sua configuração solicita um pacote por nome e faz referência a um servidor NuGet, o recurso de DSC do Chocolatey (agora na VM) captura o pacote e o instala para você. Você também pode solicitar uma versão específica de um pacote.

Na parte inferior esquerda da imagem, há um modelo de Azure Resource Manager. Neste exemplo de uso, a extensão de VM registra a VM com o servidor de pull de configuração de estado da automação do Azure (ou seja, um servidor de pull) como um nó. A configuração é armazenada no servidor de pull.
Na verdade, ele é armazenado duas vezes: uma vez como texto sem formatação e uma vez compilado como um arquivo MOF (para aqueles que conhecem tais coisas.) No portal, o MOF é uma "configuração de nó" (em vez de simplesmente "configuração"). É o artefato associado a um nó para que o nó saiba sua configuração. Os detalhes abaixo mostram como atribuir a configuração de nó ao nó.

Presumivelmente, você já está fazendo o bit na parte superior ou na maior parte dele. Criar o nuspec, compilá-lo e armazená-lo em um servidor NuGet é uma coisa pequena. E você já está gerenciando VMs. Fazer o próximo passo para a implantação contínua requer a configuração do servidor de pull (uma vez), o registro de seus nós com ele (uma vez) e a criação e o armazenamento da configuração (inicialmente). Em seguida, à medida que os pacotes são atualizados e implantados no repositório, atualize a configuração e a configuração de nó no servidor de pull (repita conforme necessário).

Se você não estiver começando com um modelo do Resource Manager, isso também será OK. Há cmdlets do PowerShell projetados para ajudá-lo a registrar suas VMs com o servidor de pull e todo o restante. Para obter mais detalhes, consulte este artigo: [integração de computadores para gerenciamento pela configuração de estado da automação do Azure](automation-dsc-onboarding.md).

## <a name="step-1-setting-up-the-pull-server-and-automation-account"></a>Etapa 1: Configurando o servidor de pull e a conta de automação

Em uma linha de comando do PowerShell autenticado (`Connect-AzureRmAccount`): (pode levar alguns minutos enquanto o servidor de pull está configurado)

```azurepowershell-interactive
New-AzureRmResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzureRmAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

Você pode colocar sua conta de automação em qualquer uma das seguintes regiões (também conhecido como local): leste dos EUA 2, Sul EUA Central, US Gov-Virgínia, Europa Ocidental, Sudeste Asiático, leste do Japão, Índia central e sudeste da Austrália, Canadá central Europa Setentrional.

## <a name="step-2-vm-extension-tweaks-to-the-resource-manager-template"></a>Etapa 2: ajustes de extensão de VM para o modelo do Resource Manager

Detalhes do registro de VM (usando a extensão de VM do DSC do PowerShell) fornecidos neste [modelo de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver).
Essa etapa registra sua nova VM com o servidor de pull na lista de nós de configuração de estado. Parte desse registro é especificar a configuração de nó a ser aplicada ao nó. Essa configuração de nó ainda não precisa existir no servidor de pull, portanto, não há problema na etapa 4, em que isso é feito pela primeira vez. Mas, aqui, na etapa 2, você precisa ter decidido o nome do nó e o nome da configuração. Neste exemplo de uso, o nó é ' isvbox ' e a configuração é ' ISVBoxConfig '. Portanto, o nome da configuração do nó (a ser especificado em Deploymenttemplate. JSON) é ' ISVBoxConfig. isvbox '.

## <a name="step-3-adding-required-dsc-resources-to-the-pull-server"></a>Etapa 3: adicionar os recursos de DSC necessários ao servidor de pull

O Galeria do PowerShell é instrumentado para instalar recursos de DSC em sua conta de automação do Azure.
Navegue até o recurso desejado e clique no botão "implantar na automação do Azure".

![Exemplo de Galeria do PowerShell](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Outra técnica adicionada recentemente ao portal do Azure permite que você extraia novos módulos ou atualize os módulos existentes. Clique no recurso conta de automação, no bloco ativos e, por fim, no bloco módulos. O ícone procurar na Galeria permite que você veja a lista de módulos na Galeria, aprofunde-se em detalhes e, por fim, importe para sua conta de automação. Essa é uma ótima maneira de manter seus módulos atualizados de tempos em tempos. E o recurso de importação verifica as dependências com outros módulos para garantir que nada fique fora de sincronia.

Ou, há a abordagem manual. A estrutura de pastas de um módulo de integração do PowerShell para um computador com Windows é um pouco diferente da estrutura de pastas esperada pela automação do Azure.
Isso requer um pequeno ajuste de sua parte. Mas não é difícil, e isso é feito apenas uma vez por recurso (a menos que você queira atualizá-lo no futuro). Para obter mais informações sobre a criação de módulos de integração do PowerShell, consulte este artigo: [criando módulos de integração para a automação do Azure](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)

- Instale o módulo de que você precisa em sua estação de trabalho da seguinte maneira:
  - Instalar o [Windows Management Framework, V5](https://aka.ms/wmf5latest) (não é necessário para o Windows 10)
  - `Install-Module –Name MODULE-NAME` < — captura o módulo do Galeria do PowerShell
- Copie a pasta do módulo de `c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME` para uma pasta temporária
- Excluir exemplos e documentação da pasta principal
- Compactar a pasta principal, nomeando o arquivo ZIP exatamente igual à pasta 
- Coloque o arquivo ZIP em um local HTTP acessível, como o armazenamento de BLOBs em uma conta de armazenamento do Azure.
- Executar este PowerShell:

  ```powershell
  New-AzureRmAutomationModule `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Name MODULE-NAME –ContentLink 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
  ```

O exemplo incluído executa essas etapas para cChoco e xNetworking. Consulte as [observações](#notes) para tratamento especial para cChoco.

## <a name="step-4-adding-the-node-configuration-to-the-pull-server"></a>Etapa 4: adicionando a configuração de nó ao servidor de pull

Não há nada de especial sobre a primeira vez que você importa a configuração para o servidor de pull e compila. Todas as importações/compilações subsequentes da mesma configuração têm exatamente a mesma aparência. Sempre que você atualizar o pacote e precisar enviá-lo para a produção, siga esta etapa depois de garantir que o arquivo de configuração está correto, incluindo a nova versão do pacote. Aqui está o arquivo de configuração e o PowerShell:

ISVBoxConfig. ps1:

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

New-ConfigurationScript. ps1:

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

Essas etapas resultam em uma nova configuração de nó denominada "ISVBoxConfig. isvbox" que está sendo colocada no servidor de pull. O nome da configuração do nó é criado como "ConfigurationName. NodeName".

## <a name="step-5-creating-and-maintaining-package-metadata"></a>Etapa 5: Criando e mantendo metadados de pacote

Para cada pacote que você coloca no repositório de pacotes, você precisa de um nuspec que o descreva.
Esse nuspec deve ser compilado e armazenado em seu servidor NuGet. Esse processo é descrito [aqui](https://docs.nuget.org/create/creating-and-publishing-a-package). Você pode usar o MyGet.org como um servidor NuGet. Eles vendem esse serviço, mas têm uma SKU inicial gratuita. Em NuGet.org, você encontrará instruções sobre como instalar seu próprio servidor NuGet para seus pacotes privados.

## <a name="step-6-tying-it-all-together"></a>Etapa 6: juntando tudo em conjunto

Cada vez que uma versão passa o controle de qualidade e é aprovada para implantação, o pacote é criado, o nuspec e o nupkg são atualizados e implantados no servidor NuGet. Além disso, a configuração (etapa 4 acima) deve ser atualizada para concordar com o novo número de versão. Ele deve ser enviado para o servidor de pull e compilado.
A partir desse ponto, cabe às VMs que dependem dessa configuração para efetuar a atualização e instalá-la. Cada uma dessas atualizações é simples-apenas uma linha ou duas do PowerShell. No caso do Azure DevOps, alguns deles são encapsulados em tarefas de compilação que podem ser encadeadas em uma compilação. Este [artigo](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) fornece mais detalhes. Este [repositório GitHub](https://github.com/Microsoft/vso-agent-tasks) detalha as várias tarefas de compilação disponíveis.

## <a name="notes"></a>Notas

Este exemplo de uso começa com uma VM de uma imagem genérica do Windows Server 2012 R2 da galeria do Azure. Você pode iniciar a partir de qualquer imagem armazenada e, em seguida, ajustar-se daí com a configuração DSC.
No entanto, a alteração da configuração que é inclusas em uma imagem é muito mais difícil do que a atualização dinâmica da configuração usando a DSC.

Você não precisa usar um modelo do Resource Manager e a extensão de VM para usar essa técnica com suas VMs. E suas VMs não precisam estar no Azure para estar em gerenciamento de CD. Tudo o que é necessário é que o Chocolatey esteja instalado e o LCM configurado na VM para que saiba onde está o servidor de pull.

É claro que, ao atualizar um pacote em uma VM que está em produção, você precisa levar essa VM fora de rotação enquanto a atualização é instalada. A maneira de fazer isso varia muito. Por exemplo, com uma VM por trás de um Azure Load Balancer, você pode adicionar uma investigação personalizada. Ao atualizar a VM, o ponto de extremidade de investigação retorna um 400. O ajuste necessário para causar essa alteração pode estar dentro de sua configuração, assim como pode ser ajustado para voltar a retornar um 200 quando a atualização for concluída.

O código-fonte completo deste exemplo de uso está neste [projeto do Visual Studio](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) no github.

## <a name="related-articles"></a>Artigos relacionados
* [Visão geral do Azure DSC de Automação](automation-dsc-overview.md)
* [Cmdlets do DSC de Automação do Azure](https://docs.microsoft.com/powershell/module/azurerm.automation#automation)
* [Máquinas de integração para gerenciamento pelo Azure DSC de Automação](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral, consulte [configuração de estado da automação do Azure](automation-dsc-overview.md)
- Para começar, consulte [introdução à configuração de estado de automação do Azure](automation-dsc-getting-started.md)
- Para saber mais sobre como compilar configurações DSC para que você possa atribuí-las aos nós de destino, consulte [compilando configurações na configuração de estado da automação do Azure](automation-dsc-compile.md)
- Para referência de cmdlet do PowerShell, consulte [cmdlets de configuração do estado de automação do Azure](/powershell/module/azurerm.automation/#automation)
- Para obter informações sobre preços, consulte [preços de configuração do estado de automação do Azure](https://azure.microsoft.com/pricing/details/automation/)
- Para ver um exemplo de como usar a configuração de estado de automação do Azure em um pipeline de implantação contínua, consulte [implantação contínua usando configuração de estado de automação do Azure e Chocolatey](automation-dsc-cd-chocolatey.md)
