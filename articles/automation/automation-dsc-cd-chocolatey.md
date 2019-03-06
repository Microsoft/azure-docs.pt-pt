---
title: Implementação contínua com uma configuração de estado da automatização do Azure com o Chocolatey
description: Implementação contínua de DevOps com o Gestor de pacotes de configuração de estado de automatização do Azure, DSC e Chocolatey.  Exemplo com o modelo de Gestor de recursos de JSON completo e a origem do PowerShell.
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 08/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b53cb65ec99637dadb16ed9d97c495571be956d7
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57451200"
---
# <a name="usage-example-continuous-deployment-to-virtual-machines-using-automation-state-configuration-and-chocolatey"></a>Exemplo de utilização: Implementação contínua para máquinas virtuais com a configuração de estado de automatização e Chocolatey

Num mundo de DevOps, existem diversas ferramentas para ajudá-lo em vários pontos no pipeline de integração contínua. A configuração de estado de automatização do Azure é um acréscimo bem-vindo de novo para as opções que podem empregar as equipas de DevOps. Este artigo demonstra a definição de cópia de segurança contínua implementação (CD) para um computador Windows. Pode expandir facilmente a técnica para incluir tantos computadores do Windows conforme necessário na função (um web site, por exemplo) e daí para funções adicionais também.

![Implementação contínua para IaaS VMs](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>Num alto nível

Há um pouco acontecendo aqui, mas Felizmente pode ser dividido em dois processos principais:

- Escrever código e testá-lo, em seguida, criar e publicar pacotes de instalação para a versão principal e secundária do sistema.
- Criar e gerir VMs que irão instalar e executar o código nos pacotes.  

Depois dos dois desses processos de núcleo estão em vigor, é um breve passo para atualizar automaticamente o pacote em execução em qualquer VM específica, conforme novas versões são criadas e implementadas.

## <a name="component-overview"></a>Visão geral de componentes

Gestores de pacote, como [apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) são muito bem conhecido no mundo do Linux, mas não tanto no mundo do Windows.
[Chocolatey](https://chocolatey.org/) é essa coisa e Scott Hanselman [blog](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) sobre o assunto é uma excelente introdução. Em resumo, o Chocolatey permite-lhe instalar pacotes a partir de um repositório central de pacotes num sistema Windows com a linha de comandos. Pode criar e gerir o seu próprio repositório e o Chocolatey pode instalar os pacotes a partir de qualquer número de repositórios que designar.

Desired State Configuration (DSC) ([descrição geral](/powershell/dsc/overview)) é uma ferramenta do PowerShell que permite declarar a configuração que pretende para uma máquina. Por exemplo, pode dizer, "eu quero que o Chocolatey instalado, eu quero que o IIS instalado, quero que a porta 80 aberta, quero versão 1.0.0 do meu site instalado." O Gestor de DSC configuração Local (LCM) implementa essa configuração. Um servidor de solicitação de DSC contém um repositório de configurações para as suas máquinas. O LCM em cada máquina verifica periodicamente para ver se a sua configuração corresponde a configuração armazenada. Ela pode comunicou o estado de ou tentar trazer de volta a máquina em alinhamento com a configuração armazenado. Pode editar a configuração armazenada no servidor de solicitação para fazer com que uma máquina ou um conjunto de máquinas que entrem em alinhamento com a configuração foi alterado.

A automatização do Azure é um serviço gerido do Microsoft Azure que permite-lhe automatizar várias tarefas, utilizando runbooks, nós, as credenciais, recursos e ativos como as agendas e variáveis globais.
A configuração de estado de automatização do Azure amplia esse recurso de automatização para incluir ferramentas de DSC do PowerShell. Aqui é uma excelente [descrição geral](automation-dsc-overview.md).

Um recurso de DSC é um módulo de código que tenha capacidades específicas, como a gestão de rede, Active Directory ou o SQL Server. O recurso de DSC Chocolatey sabe como aceder a um servidor do NuGet (entre outras), transferir pacotes, instalar pacotes e assim por diante. Existem muitos outros recursos de DSC na [galeria do PowerShell](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title).
Estes módulos são instalados no seu servidor de solicitação de configuração do Azure Automation Estado (por si) para que possam ser utilizados por suas configurações.

Modelos do Resource Manager proporcionam uma forma declarativa de gerar a sua infraestrutura - coisas como redes, sub-redes, segurança de rede e encaminhamento, balanceadores de carga, NICs, VMs e assim por diante. Aqui está uma [artigo](../azure-resource-manager/resource-manager-deployment-model.md) que compara o modelo de implementação Resource Manager (declarativo) com o modelo de implementação de gestão de serviço do Azure (ASM ou clássico) (imperativo) e aborda os principais fornecedores de recursos, computação, armazenamento e rede.

Uma funcionalidade-chave de um modelo do Resource Manager é a possibilidade de instalar uma extensão de VM na VM à medida que está aprovisionado. Uma extensão de VM tem capacidades específicas, como executar um script personalizado, instalar o software antivírus ou executar um script de configuração de DSC. Existem vários outros tipos de extensões de VM.

## <a name="quick-trip-around-the-diagram"></a>Rápido viagem ao redor de diagrama

A partir de na parte superior, escrever seu código, criar e testar, em seguida, criar um pacote de instalação.
O Chocolatey pode processar vários tipos de pacotes de instalação, por exemplo, o MSI, MSU, ZIP. E tem todo o potencial do PowerShell para fazer a instalação real se recursos nativos de Chocolateys não são bem até ele. Coloque o pacote em algum lugar acessível – um repositório de pacotes. Este exemplo de utilização utiliza uma pasta pública numa conta de armazenamento de Blobs do Azure, mas pode ser em qualquer lugar. O Chocolatey nativamente funciona com servidores NuGet e alguns outros para a gestão de metadados do pacote. [Este artigo](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) descreve as opções. Este exemplo de utilização utiliza NuGet. Um Nuspec é metadados sobre os pacotes. O Nuspec é "compilado" em do NuPkg e armazenado num servidor do NuGet. Quando a configuração solicita um pacote por nome e faz referência a um servidor do NuGet, o recurso de DSC Chocolatey (agora na VM) captura o pacote e instala por si. Também pode solicitar uma versão específica de um pacote.

Na parte inferior esquerda da imagem, há um modelo Azure Resource Manager. Neste exemplo de utilização, a extensão de VM registra a VM com o servidor de solicitação de configuração de estado de automatização do Azure (ou seja, um servidor de solicitação) como um nó. A configuração é armazenada no servidor de solicitação.
Na verdade, é armazenada duas vezes: uma vez como texto simples e uma vez que compilado como um ficheiro MOF (para aqueles que precisa saber sobre assuntos.) No portal, o MOF é uma "configuração de nó" (em vez de simplesmente "configuração"). É o artefacto que está associado um nó para que o nó saberá a respetiva configuração. Detalhes abaixo mostram como atribuir a configuração do nó a nó.

Provavelmente já estiver fazendo o bit na parte superior, ou o máximo proveito dele. O nuspec a criar, compilar e armazenando-o num servidor do NuGet são algo pequeno. E já estiver a gerir as VMs. Levando a próxima etapa para a implementação contínua requer a configuração de servidor de solicitação (uma vez), os nós a registar com o mesmo (uma vez) e criar e armazenar a configuração lá (inicialmente). Em seguida, como pacotes são atualizados e implementados para o repositório, atualize a configuração e a configuração do nó no servidor de solicitação (Repita conforme necessário).

Se não estiver começando com um modelo do Resource Manager, que também está OK. Existem cmdlets do PowerShell concebido para ajudar a registar as suas VMs com o servidor de solicitação e tudo o resto. Para obter mais detalhes, consulte este artigo: [Integrar computadores para gestão de configuração de estado de automatização do Azure](automation-dsc-onboarding.md).

## <a name="step-1-setting-up-the-pull-server-and-automation-account"></a>Passo 1: Como configurar a conta de servidor e automatização pull

Num autenticado (`Connect-AzureRmAccount`) linha de comandos do PowerShell: (pode demorar alguns minutos enquanto o servidor de solicitação está configurado)

```azurepowershell-interactive
New-AzureRmResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzureRmAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

Pode colocar a sua conta de automatização em qualquer uma das seguintes regiões (também conhecido como localização): E.U.A. Leste 2, Centro-Sul dos E.U.A., US Gov Virginia, Europa Ocidental, Sudeste asiático, leste do Japão, Índia Central e Sudeste da Austrália, Canadá Central, Europa do Norte.

## <a name="step-2-vm-extension-tweaks-to-the-resource-manager-template"></a>Passo 2: Ajustes de extensão VM para o modelo do Resource Manager

Detalhes para o registo VM (com a extensão de VM de DSC de PowerShell) fornecidos nesta [modelo de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver).
Este passo registra sua nova VM com o servidor de solicitação na lista de nós de configuração de estado. Parte desse Registro é especificar a configuração do nó a ser aplicado ao nó. Esta configuração de nó não tem ainda existe no servidor de solicitação, pelo que é OK passo 4 é onde isso é feito pela primeira vez. Mas aqui no passo 2 é necessário decidir o nome do nó e o nome da configuração. Neste exemplo de utilização, o nó é 'isvbox' e a configuração é 'ISVBoxConfig'. Portanto, o nome de configuração do nó (para ser especificada no DeploymentTemplate.json) é 'ISVBoxConfig.isvbox'.

## <a name="step-3-adding-required-dsc-resources-to-the-pull-server"></a>Passo 3: Adicionar recursos de DSC necessários para o servidor de solicitação

A galeria do PowerShell foi instrumentada para instalar os recursos de DSC na sua conta de automatização do Azure.
Navegue para o recurso que pretende e clique no botão "Implementar a automatização do Azure".

![Exemplo da galeria do PowerShell](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Outra técnica recentemente adicionada ao Portal do Azure permite-lhe obter novos módulos ou atualizar módulos existentes. Clique para aceder o recursos da conta de automatização, o mosaico de ativos e, finalmente, o mosaico de módulos. O ícone de Galeria de procura permite-lhe ver a lista de módulos da galeria, desagregar para obter detalhes e, por fim, importar para a sua conta de automatização. Esta é uma excelente forma de manter os seus módulos atualizados periodicamente. Além disso, a funcionalidade de importação verifica as dependências com outros módulos para garantir que nada ficará fora de sincronia.

Ou, se estiver a abordagem manual. A estrutura de pastas de um módulo de integração do PowerShell para um computador Windows é um pouco diferente da estrutura de pastas esperada pela automatização do Azure.
Isso exige um pequeno ajuste da sua parte. Mas não é difícil, e isso é feito apenas uma vez por recurso (a menos que queira atualizá-lo no futuro.) Para obter mais informações sobre a criação de módulos de integração do PowerShell, consulte este artigo: [Criação de módulos de integração para a automatização do Azure](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)

- Instale o módulo que tem na sua estação de trabalho, da seguinte forma:
  - Instale [Windows Management Framework, v5](https://aka.ms/wmf5latest) (não necessária para o Windows 10)
  - `Install-Module –Name MODULE-NAME`    < — capta o módulo da galeria do PowerShell
- Copie a pasta de módulo de `c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME` para uma pasta temporária
- Eliminar amostras e documentação da pasta principal
- Zip na pasta principal, o ficheiro ZIP de nomenclatura exatamente o mesmo que a pasta 
- Coloca o ficheiro ZIP num local acessível do HTTP, como armazenamento de BLOBs numa conta de armazenamento do Azure.
- Execute este PowerShell:

  ```powershell
  New-AzureRmAutomationModule `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Name MODULE-NAME –ContentLink 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
  ```

O exemplo incluído executa estes passos para cChoco e xNetworking. Consulte a [notas](#notes) para tratamento especial para cChoco.

## <a name="step-4-adding-the-node-configuration-to-the-pull-server"></a>Passo 4: Adicionar a configuração de nó para o servidor de solicitação

Não há nada de especial sobre a primeira vez, importar a configuração para o servidor de solicitação e a compilação. Todos os subsequentes import/compila da configuração do mesmo ter um aspeto exatamente o mesmo. Sempre que atualizar o pacote e tem de enviá-lo para produção efetue este passo depois de assegurar que o ficheiro de configuração está correto – incluindo a nova versão do pacote. Este é o ficheiro de configuração e o PowerShell:

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

New-ConfigurationScript.ps1:

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

Resultado essas etapas numa nova configuração de nó com o nome "ISVBoxConfig.isvbox", que está a ser colocados no servidor de solicitação. O nome de configuração do nó foi concebido como "configurationName.nodeName".

## <a name="step-5-creating-and-maintaining-package-metadata"></a>Passo 5: Criação e manutenção de metadados do pacote

Para cada pacote que coloca no repositório do pacote, terá um nuspec que a descreve.
Esse nuspec deve ser compilado e armazenado no seu servidor do NuGet. Este processo é descrito [aqui](https://docs.nuget.org/create/creating-and-publishing-a-package). Pode usar MyGet.org como um servidor do NuGet. Vender este serviço, mas têm uma SKU que é gratuito de arranque. Em NuGet.org, encontrará instruções sobre como instalar o seu próprio servidor do NuGet para seus pacotes privadas.

## <a name="step-6-tying-it-all-together"></a>Passo 6: Juntando as peças

Sempre que uma versão passa o controle de qualidade e é aprovada para implementação, o pacote criado nuspec e nupkg atualizado e implementados no servidor do NuGet. Além disso, a configuração (passo 4 acima) tem de ser atualizada para os Concordo com o novo número de versão. Tem de ser enviado para o servidor de solicitação e compilado.
Daí em diante, cabe para as VMs que dependem de que a configuração para extrair a atualização e instalá-lo. Cada uma destas atualizações são simples - apenas uma ou duas linhas do PowerShell. No caso do Azure DevOps, alguns deles são encapsuladas em tarefas de compilação que podem ser encadeadas numa compilação. Isso [artigo](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) fornece mais detalhes. Isso [repositório do GitHub](https://github.com/Microsoft/vso-agent-tasks) fornece detalhes sobre as diversas tarefas de compilação disponíveis.

## <a name="notes"></a>Notas

Neste exemplo de utilização começa com uma VM a partir de uma imagem do Windows Server 2012 R2 genérica da galeria do Azure. Pode iniciar a partir de qualquer imagem armazenada e, em seguida, ajustar a partir daí, com a configuração de DSC.
No entanto, a alteração de configuração que está embutida numa imagem é muito mais difícil do que dinamicamente a atualizar a configuração com o DSC.

Não tem de utilizar um modelo do Resource Manager e a extensão VM para utilizar esta técnica com as suas VMs. E as suas VMs não precisam de ser no Azure para estar sob gestão do CD. Tudo o que é necessário é que o Chocolatey seja instalado e configurado o LCM na VM, para que ele saiba onde está o servidor de solicitação.

É claro, quando atualizar um pacote numa VM que esteja em produção, precisa colocar essa VM da rotação, enquanto a atualização está instalada. Como fazer isso varia muito. Por exemplo, com uma VM por trás de um balanceador de carga do Azure, pode adicionar uma sonda personalizada. Ao atualizar a VM, tem o ponto final da sonda devolver um 400. O ajuste necessário para fazer com que esta alteração pode ser dentro de sua configuração, como pode o ajuste de alterá-la para retornar um 200 assim que a atualização estiver concluída.

Código-fonte completo para este exemplo de utilização está em [este projeto do Visual Studio](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) no GitHub.

## <a name="related-articles"></a>Artigos relacionados
* [Descrição geral de DSC de automatização do Azure](automation-dsc-overview.md)
* [Cmdlets de DSC de automatização do Azure](https://docs.microsoft.com/powershell/module/azurerm.automation#automation)
* [Integrar computadores para gestão pelo DSC de automatização do Azure](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Passos Seguintes

- Para uma descrição geral, consulte [configuração de estado de automatização do Azure](automation-dsc-overview.md)
- Para começar a utilizar, consulte o artigo [introdução à configuração de estado de automatização do Azure](automation-dsc-getting-started.md)
- Para obter informações sobre como compilar configurações de DSC para que pode atribuí-las a nós de destino, consulte [compilar configurações na configuração de estado de automatização do Azure](automation-dsc-compile.md)
- Para referência de cmdlets do PowerShell, consulte [cmdlets de configuração de estado de automatização do Azure](/powershell/module/azurerm.automation/#automation)
- Para obter informações sobre preços, consulte [preços de configuração de estado de automatização do Azure](https://azure.microsoft.com/pricing/details/automation/)
- Para ver um exemplo do uso de configuração de estado de automatização do Azure num pipeline de implementação contínua, consulte [contínua através do Azure Automation estado de configuração da implementação e Chocolatey](automation-dsc-cd-chocolatey.md)
