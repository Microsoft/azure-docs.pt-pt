---
title: Como criar políticas de Configuração de Convidado para o Windows
description: Saiba como criar uma política de configuração de hóspedes Azure Policy para windows.
ms.date: 08/17/2020
ms.topic: how-to
ms.openlocfilehash: 563b178b9ba92125967c779b59a78a8e105ec744
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92542867"
---
# <a name="how-to-create-guest-configuration-policies-for-windows"></a>Como criar políticas de Configuração de Convidado para o Windows

Antes de criar definições de política personalizadas, é uma boa ideia ler a informação conceptual geral na página [Azure Policy Guest Configuration](../concepts/guest-configuration.md).
 
Para aprender a criar políticas de Configuração de Hóspedes para Linux, consulte a página [Como criar políticas de Configuração de Hóspedes para Linux](./guest-configuration-create-linux.md)

Ao auditar o Windows, a Configuração de Convidado utiliza um módulo de recurso [Desired State Configuration](/powershell/scripting/dsc/overview/overview) (DSC) para criar o ficheiro de configuração. A configuração DSC define a condição em que o computador deverá estar. Se a avaliação da configuração falhar, a auditoria do efeito **políticoIfNotExists** é ativada e a máquina é considerada **incompatível** .

[A configuração do hóspede Azure Policy](../concepts/guest-configuration.md) só pode ser usada para auditar definições dentro de máquinas. A reparação de configurações dentro das máquinas ainda não está disponível.

Utilize as seguintes ações para criar a sua própria configuração para validar o estado de uma máquina Azure ou não-Azure.

> [!IMPORTANT]
> A extensão de Configuração de Convidado é necessária para realizar auditorias nas máquinas virtuais do Azure.
> Para implementar a extensão em escala em todas as máquinas do Windows, atribua as seguintes definições de política: `Deploy prerequisites to enable Guest Configuration Policy on Windows VMs`

## <a name="install-the-powershell-module"></a>Instalar o módulo do PowerShell

O módulo de Configuração do Hóspede automatiza o processo de criação de conteúdo personalizado, incluindo:

- Criando um artefacto de conteúdo de configuração de hóspedes (.zip)
- Teste automatizado do artefacto
- Criação de uma definição de política
- Publicar a política

O módulo pode ser instalado numa máquina que executa o Windows, macOS ou Linux com o PowerShell 6.2 ou posteriormente a funcionar localmente, ou com a [Azure Cloud Shell](https://shell.azure.com), ou com a [imagem Azure PowerShell Core Docker](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> A compilação de configurações ainda não está suportada no Linux.

### <a name="base-requirements"></a>Requisitos de base

Sistemas operativos onde o módulo pode ser instalado:

- Linux
- macOS
- Windows

O módulo de recursos de configuração do hóspede requer o seguinte software:

- PowerShell 6.2 ou mais tarde. Se não estiver ainda instalado, siga [estas instruções](/powershell/scripting/install/installing-powershell).
- Azure PowerShell 1.5.0 ou superior. Se não estiver ainda instalado, siga [estas instruções](/powershell/azure/install-az-ps).
  - Apenas são necessários os módulos Az 'Az.Accounts' e 'Az.Resources'.

### <a name="install-the-module"></a>Instale o módulo

Para instalar o módulo **GuestConfiguration** no PowerShell:

1. A partir de um pedido PowerShell, executar o seguinte comando:

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. Validar que o módulo foi importado:

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="guest-configuration-artifacts-and-policy-for-windows"></a>Artefactos de configuração de hóspedes e política para Windows

A Configuração do Hóspede utiliza a Configuração do Estado Desejada powerShell como uma abstração linguística para escrever o que auditar no Windows. O agente carrega um caso autónomo de PowerShell 6.2, pelo que não há conflito com a utilização do PowerShell DSC no Windows PowerShell 5.1, e não há necessidade de pré-instalar o PowerShell 6.2 ou mais tarde.

Para uma visão geral dos conceitos e terminologia do DSC, consulte [a Visão Geral do DSC powerShell](/powershell/scripting/dsc/overview/overview).

### <a name="how-guest-configuration-modules-differ-from-windows-powershell-dsc-modules"></a>Como os módulos de configuração dos hóspedes diferem dos módulos DSC do Windows PowerShell

Quando a Configuração de Hóspedes audita uma máquina, a sequência de eventos é diferente da do Windows PowerShell DSC.

1. O agente corre primeiro `Test-TargetResource` para determinar se a configuração está no estado correto.
1. O valor booleano devolvido pela função determina se o estado do Gestor de Recursos Azure para a Atribuição do Hóspede deve ser conforme/não conforme.
1. O fornecedor corre `Get-TargetResource` para devolver o estado atual de cada configuração para que haja detalhes disponíveis tanto sobre o motivo pelo qual uma máquina não está em conformidade como para confirmar que o estado atual está em conformidade.

Os parâmetros na Política Azure que passam valores para as atribuições de Configuração de Convidados devem ser do tipo _de corda._ Não é possível passar matrizes através de parâmetros, mesmo que o recurso DSC suporte matrizes.

### <a name="get-targetresource-requirements"></a>requisitos Get-TargetResource

A função `Get-TargetResource` tem requisitos especiais para a Configuração do Hóspede que não foram necessários para a Configuração do Estado do Windows Desired.

- O haxixe que é devolvido deve incluir uma propriedade chamada **Reasons** .
- A propriedade Reasons deve ser uma matriz.
- Cada item na matriz deve ser um haxixe com as teclas chamadas **Código** e **Frase** .

A propriedade Reasons é utilizada pelo serviço para normalizar a forma como a informação é apresentada quando uma máquina está fora de conformidade. Pode pensar em cada item em Reasons como uma "razão" de que o recurso não está em conformidade. A propriedade é uma matriz porque um recurso pode estar fora de conformidade por mais de uma razão.

As propriedades **Código** e **Frase** são esperadas pelo serviço. Ao autorizar um recurso personalizado, desenrida o texto (tipicamente estaladudo) que pretende mostrar como a razão pela qual o recurso não está em conformidade como o valor da **Frase** . **O código** tem requisitos específicos de formatação para que a comunicação possa apresentar claramente informações sobre o recurso utilizado para fazer a auditoria. Esta solução torna a configuração do hóspede extensível. Qualquer comando pode ser executado desde que a saída possa ser devolvida como um valor de corda para a propriedade **Frase.**

- **Código** (cadeia): O nome do recurso, repetido e, em seguida, um nome curto sem espaços como identificador pela razão. Estes três valores devem ser delimitados pelo cólon sem espaços.
  - Um exemplo seria `registry:registry:keynotpresent`
- **Frase** (cadeia): Texto legível pelo homem para explicar por que a definição não é compatível.
  - Um exemplo seria `The registry key $key is not present on the machine.`

```powershell
$reasons = @()
$reasons += @{
  Code = 'Name:Name:ReasonIdentifer'
  Phrase = 'Explain why the setting is not compliant'
}
return @{
    reasons = $reasons
}
```

A propriedade Reasons deve ser adicionada ao esquema MOF para o recurso como uma classe incorporada.

```mof
[ClassVersion("1.0.0.0")] 
class Reason
{
    [Read] String Phrase;
    [Read] String Code;
};

[ClassVersion("1.0.0.0"), FriendlyName("ResourceName")]
class ResourceName : OMI_BaseResource
{
    [Key, Description("Example description")] String Example;
    [Read, EmbeddedInstance("Reason")] String Reasons[];
};
```

### <a name="configuration-requirements"></a>Requisitos de configuração

O nome da configuração personalizada deve ser consistente em todo o lado. O nome do ficheiro .zip para o pacote de conteúdo, o nome de configuração no ficheiro MOF e o nome de atribuição do convidado no modelo Azure Resource Manager (modelo ARM), deve ser o mesmo.

### <a name="scaffolding-a-guest-configuration-project"></a>Andaime um projeto de configuração de hóspedes

Os desenvolvedores que gostariam de acelerar o processo de começar e trabalhar a partir do código de amostra pode instalar um projeto comunitário chamado **Guest Configuration Project** . O projeto instala um modelo para o módulo [Plaster](https://github.com/powershell/plaster) PowerShell. Esta ferramenta pode ser usada para empaar o projeto, incluindo uma configuração de trabalho e recurso de amostra, e um conjunto de testes [de Pester](https://github.com/pester/pester) para validar o projeto. O modelo também inclui corredores de tarefas para Código de Estúdio Visual para automatizar a construção e validar o pacote de Configuração de Convidados. Para mais informações, consulte o [Projeto de Configuração do Convidado do](https://github.com/microsoft/guestconfigurationproject)projeto GitHub.

Para obter mais informações sobre o trabalho com configurações em geral, consulte [Write, Compile e Apply a Configuration](/powershell/scripting/dsc/configurations/write-compile-apply-configuration).

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>Conteúdo esperado de um artefacto de configuração de hóspedes

O pacote completo é utilizado pela Configuração do Hóspede para criar as definições de Política Azure. O pacote consiste em:

- A configuração compilada do DSC como MOF
- Pasta de módulos
  - Módulo de Configuração de Hóspedes
  - Módulo DscNativeResources
  - (Janelas) Módulos de recursos DSC exigidos pelo MOF

Os cmdlets PowerShell ajudam na criação do pacote.
Não é necessária nenhuma pasta de nível de raiz ou pasta de versão.
O formato do pacote deve ser um ficheiro .zip.

### <a name="storing-guest-configuration-artifacts"></a>Armazenar artefactos de configuração de hóspedes

A embalagem .zip deve ser armazenada num local acessível pelas máquinas virtuais geridas.
Exemplos incluem repositórios GitHub, um Azure Repo, ou armazenamento Azure. Se preferir não tornar o pacote público, pode incluir um [token SAS](../../../storage/common/storage-sas-overview.md) no URL. Também pode implementar [o ponto final de serviço](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) para máquinas numa rede privada, embora esta configuração se aplique apenas ao acesso ao pacote e não à comunicação com o serviço.

## <a name="step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows"></a>Passo a passo, criando uma política de auditoria de configuração personalizada para windows

Crie uma configuração DSC para auditar as definições. O exemplo de script powerShell a seguir cria uma configuração chamada **AuditBitLocker,** importa o módulo de recursos **PsDscResources** e utiliza o `Service` recurso para auditar para um serviço de execução. O script de configuração pode ser executado a partir de uma máquina Windows ou macOS.

```powershell
# Add PSDscResources module to environment
Install-Module 'PSDscResources'

# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Node AuditBitlocker {
      Service 'Ensure BitLocker service is present and running'
      {
          Name = 'BDESVC'
          Ensure = 'Present'
          State = 'Running'
      }
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker ./Config
```

Guarde este ficheiro com o nome `config.ps1` na pasta do projeto. Execute-o no PowerShell executando `./config.ps1` no terminal. Um novo ficheiro mof é criado.

O `Node AuditBitlocker` comando não é tecnicamente necessário, mas produz um ficheiro nomeado em vez do `AuditBitlocker.mof` padrão, `localhost.mof` . Ter o nome do ficheiro .mof seguir a configuração facilita a organização de muitos ficheiros ao operar em escala.

Uma vez compilados os ficheiros de suporte, os ficheiros de apoio devem ser embalados em conjunto. O pacote completo é utilizado pela Configuração do Hóspede para criar as definições de Política Azure.

O `New-GuestConfigurationPackage` cmdlet cria o pacote. Os módulos necessários pela configuração devem estar disponíveis em `$Env:PSModulePath` . Parâmetros do `New-GuestConfigurationPackage` cmdlet ao criar conteúdo do Windows:

- **Nome** : Nome do pacote de configuração do hóspede.
- **Configuração** : Documento de configuração DSC compilado no caminho completo.
- **Caminho** : Caminho da pasta de saída. Este parâmetro é opcional. Se não for especificado, o pacote é criado no diretório atual.

Executar o seguinte comando para criar um pacote utilizando a configuração dada no passo anterior:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditBitlocker' `
  -Configuration './Config/AuditBitlocker.mof'
```

Depois de criar o pacote de Configuração, mas antes de publicá-lo no Azure, pode testar o pacote a partir da sua estação de trabalho ou ambiente de integração contínua e implementação contínua (CI/CD). O cmdlet GuestConfiguration `Test-GuestConfigurationPackage` inclui o mesmo agente no seu ambiente de desenvolvimento que é utilizado dentro das máquinas Azure. Utilizando esta solução, pode fazer testes de integração localmente antes de lançar para ambientes de nuvem faturada.

Uma vez que o agente está realmente a avaliar o ambiente local, na maioria dos casos você precisa executar o teste-cmdlet na mesma plataforma de SO que você planeia auditar. O teste utiliza apenas módulos incluídos no pacote de conteúdos.

Parâmetros do `Test-GuestConfigurationPackage` cmdlet:

- **Nome** : Nome da política de configuração do hóspede.
- **Parâmetro** : Parâmetros de política fornecidos em formato haxixe.
- **Caminho** : Percurso completo do pacote de Configuração de Convidados.

Executar o seguinte comando para testar o pacote criado pelo passo anterior:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditBitlocker.zip
```

O cmdlet também suporta a entrada do gasoduto PowerShell. Encaneie a saída do `New-GuestConfigurationPackage` cmdlet até ao `Test-GuestConfigurationPackage` cmdlet.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditBitlocker -Configuration ./Config/AuditBitlocker.mof | Test-GuestConfigurationPackage
```

O próximo passo é publicar o ficheiro no Azure Blob Storage. O comando `Publish-GuestConfigurationPackage` requer o `Az.Storage` módulo.

```azurepowershell-interactive
Publish-GuestConfigurationPackage -Path ./AuditBitlocker.zip -ResourceGroupName myResourceGroupName -StorageAccountName myStorageAccountName
```

Uma vez criado e carregado um pacote de política personalizada de Configuração de Convidados, crie a definição de política de Configuração de Convidados. O `New-GuestConfigurationPolicy` cmdlet pega num pacote de política personalizado e cria uma definição de política.

Parâmetros do `New-GuestConfigurationPolicy` cmdlet:

- **ContentUri** : Public http http(s) uri do pacote de conteúdo de configuração de hóspedes.
- **DisplayName** : Nome do visor da política.
- **Descrição** : Descrição da política.
- **Parâmetro** : Parâmetros de política fornecidos em formato haxixe.
- **Versão** : Versão política.
- **Caminho** : Caminho de destino onde são criadas definições políticas.
- **Plataforma** : Plataforma-alvo (Windows/Linux) para a política de configuração de hóspedes e pacote de conteúdo.
- **Tag** adiciona um ou mais filtros de etiqueta à definição de política
- **Categoria** define o campo de metadados de categoria na definição de política

O exemplo a seguir cria as definições de política num caminho especificado a partir de um pacote de política personalizado:

```azurepowershell-interactive
New-GuestConfigurationPolicy `
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path './policies' `
    -Platform 'Windows' `
    -Version 1.0.0 `
    -Verbose
```

Os seguintes ficheiros são criados `New-GuestConfigurationPolicy` por:

- **auditIfNotExists.js**

A saída do cmdlet devolve um objeto que contém o nome de visualização da iniciativa e o caminho dos ficheiros de política.

Por fim, publique as definições de política utilizando o `Publish-GuestConfigurationPolicy` cmdlet. O cmdlet tem apenas o parâmetro **Path** que aponta para a localização dos ficheiros JSON criados por `New-GuestConfigurationPolicy` .

Para executar o comando Publicar, precisa de ter acesso para criar políticas no Azure. Os requisitos específicos de autorização estão documentados na página [Azure Policy Overview.](../overview.md) O melhor papel incorporado é o **Contribuinte para a Política de Recursos.**

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions'
```

O `Publish-GuestConfigurationPolicy` cmdlet aceita o caminho a partir do gasoduto PowerShell. Esta funcionalidade significa que pode criar os ficheiros de política e publicá-los num único conjunto de comandos canalizados.

```azurepowershell-interactive
New-GuestConfigurationPolicy `
 -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit BitLocker service.' `
  -Description 'Audit if the BitLocker service is not enabled on Windows machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
```

Com a política criada em Azure, o último passo é atribuir a definição. Veja como atribuir a definição ao [Portal](../assign-policy-portal.md), [Azure CLI](../assign-policy-azurecli.md)e [Azure PowerShell](../assign-policy-powershell.md).

### <a name="filtering-guest-configuration-policies-using-tags"></a>Filtrar as políticas de configuração do hóspede usando Tags

As definições de política criadas por cmdlets no módulo de Configuração do Hóspede podem, opcionalmente, incluir um filtro para tags. O parâmetro **tag** de `New-GuestConfigurationPolicy` suporte uma variedade de hashtables contendo conjuntos de etiquetas individuais inteiras. As etiquetas são adicionadas à `If` secção da definição de política e não podem ser modificadas por uma atribuição de política.

Um exemplo de uma definição de política que filtra para tags é dado abaixo.

```json
"if": {
  "allOf" : [
    {
      "allOf": [
        {
          "field": "tags.Owner",
          "equals": "BusinessUnit"
        },
        {
          "field": "tags.Role",
          "equals": "Web"
        }
      ]
    },
    {
      // Original Guest Configuration content
    }
  ]
}
```

### <a name="using-parameters-in-custom-guest-configuration-policy-definitions"></a>Usando parâmetros nas definições de política de configuração personalizada do hóspede

A Configuração do Hóspede suporta propriedades dominantes de uma Configuração no tempo de execução. Esta funcionalidade significa que os valores no ficheiro MOF na embalagem não têm de ser considerados estáticos. Os valores de sobreposição são fornecidos através da Política Azure e não têm impacto na forma como as Configurações são da autoria ou compiladas.

Os cmdlets `New-GuestConfigurationPolicy` e `Test-GuestConfigurationPolicyPackage` incluem um parâmetro chamado **Parâmetro** . Este parâmetro requer uma definição de haxixe, incluindo todos os detalhes sobre cada parâmetro e cria as secções necessárias de cada ficheiro utilizado para a definição de Política Azure.

O exemplo a seguir cria uma definição de política para auditar um serviço, onde o utilizador seleciona a partir de uma lista no momento da atribuição de políticas.

```azurepowershell-interactive
# This DSC Resource text:
Service 'UserSelectedNameExample'
      {
          Name = 'ParameterValue'
          Ensure = 'Present'
          State = 'Running'
      }

# Would require the following hashtable:
$PolicyParameterInfo = @(
    @{
        Name = 'ServiceName'                                            # Policy parameter name (mandatory)
        DisplayName = 'windows service name.'                           # Policy parameter display name (mandatory)
        Description = "Name of the windows service to be audited."      # Policy parameter description (optional)
        ResourceType = "Service"                                        # DSC configuration resource type (mandatory)
        ResourceId = 'UserSelectedNameExample'                                   # DSC configuration resource id (mandatory)
        ResourcePropertyName = "Name"                                   # DSC configuration resource property name (mandatory)
        DefaultValue = 'winrm'                                          # Policy parameter default value (optional)
        AllowedValues = @('BDESVC','TermService','wuauserv','winrm')    # Policy parameter allowed values (optional)
    }
)

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Windows Service.' `
    -Description 'Audit if a Windows Service is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Parameter $PolicyParameterInfo `
    -Version 1.0.0
```

## <a name="extending-guest-configuration-with-third-party-tools"></a>Ampliação da configuração do hóspede com ferramentas de terceiros

Os pacotes de artefactos para configuração de hóspedes podem ser estendidos para incluir ferramentas de terceiros.
O alargamento da configuração do hóspede requer o desenvolvimento de dois componentes.

- Um recurso de configuração do estado desejado que lida com toda a atividade relacionada com a gestão da ferramenta de terceiros
  - Instalar
  - Invocar
  - Converter saída
- Conteúdo no formato correto para a ferramenta consumir de forma nativa

O recurso DSC requer desenvolvimento personalizado se uma solução comunitária já não existir.
As soluções comunitárias podem ser descobertas pesquisando na PowerShell Gallery para obter a etiqueta [GuestConfiguration](https://www.powershellgallery.com/packages?q=Tags%3A%22GuestConfiguration%22).

> [!Note]
> A extensibilidade da configuração do hóspede é um cenário de "trazer a sua própria licença". Certifique-se de que cumpriu os termos e condições de quaisquer ferramentas de terceiros antes da sua utilização.

Depois de o recurso DSC ter sido instalado no ambiente de desenvolvimento, utilize o parâmetro **FilesToInclude** `New-GuestConfigurationPackage` para incluir conteúdo para a plataforma de terceiros no artefacto do conteúdo.

### <a name="step-by-step-creating-a-content-artifact-that-uses-third-party-tools"></a>Passo a passo, criando um artefacto de conteúdo que usa ferramentas de terceiros

Apenas o `New-GuestConfigurationPackage` cmdlet requer uma alteração da orientação passo a passo para os artefactos de conteúdo DSC. Para este exemplo, utilize o `gcInSpec` módulo para estender a Configuração do Hóspede para auditar máquinas Windows utilizando a plataforma InSpec em vez do módulo incorporado utilizado no Linux. O módulo comunitário é mantido como um [projeto de código aberto no GitHub.](https://github.com/microsoft/gcinspec)

Instale os módulos necessários no seu ambiente de desenvolvimento:

```azurepowershell-interactive
# Update PowerShellGet if needed to allow installing PreRelease versions of modules
Install-Module PowerShellGet -Force

# Install GuestConfiguration module prerelease version
Install-Module GuestConfiguration -allowprerelease

# Install commmunity supported gcInSpec module
Install-Module gcInSpec
```

Primeiro, crie o ficheiro YaML utilizado pela InSpec. O ficheiro fornece informações básicas sobre o ambiente. Um exemplo é dado abaixo:

```YaML
name: wmi_service
title: Verify WMI service is running
maintainer: Microsoft Corporation
summary: Validates that the Windows Service 'winmgmt' is running
copyright: Microsoft Corporation
license: MIT
version: 1.0.0
supports:
  - os-family: windows
```

Guarde este ficheiro numa `wmi_service.yml` pasta nomeada no seu `wmi_service` diretório de projeto.

Em seguida, crie o ficheiro Ruby com a abstração linguística InSpec usada para auditar a máquina.

```Ruby
control 'wmi_service' do
  impact 1.0
  title 'Verify windows service: winmgmt'
  desc 'Validates that the service, is installed, enabled, and running'

  describe service('winmgmt') do
    it { should be_installed }
    it { should be_enabled }
    it { should be_running }
  end
end

```

Guarde este ficheiro `wmi_service.rb` numa nova pasta chamada dentro do `controls` `wmi_service` diretório.

Finalmente, crie uma configuração, importe o módulo de recursos **GuestConfiguration** e use o `gcInSpec` recurso para definir o nome do perfil InSpec.

```powershell
# Define the configuration and import GuestConfiguration
Configuration wmi_service
{
    Import-DSCResource -Module @{ModuleName = 'gcInSpec'; ModuleVersion = '2.1.0'}
    node 'wmi_service'
    {
        gcInSpec wmi_service
        {
            InSpecProfileName       = 'wmi_service'
            InSpecVersion           = '3.9.3'
            WindowsServerVersion    = '2016'
        }
    }
}

# Compile the configuration to create the MOF files
wmi_service -out ./Config
```

Deve agora ter uma estrutura de projeto como abaixo:

```file
/ wmi_service
    / Config
        wmi_service.mof
    / wmi_service
        wmi_service.yml
        / controls
            wmi_service.rb 
```

Os ficheiros de apoio devem ser embalados em conjunto. O pacote completo é utilizado pela Configuração do Hóspede para criar as definições de Política Azure.

O `New-GuestConfigurationPackage` cmdlet cria o pacote. Para conteúdos de terceiros, utilize o parâmetro **FilesToInclude** para adicionar o conteúdo InSpec à embalagem. Você não precisa especificar o **ChefProfilePath** como para pacotes Linux.

- **Nome** : Nome do pacote de configuração do hóspede.
- **Configuração** : Documento de configuração compilado no caminho completo.
- **Caminho** : Caminho da pasta de saída. Este parâmetro é opcional. Se não for especificado, o pacote é criado no diretório atual.
- **FilesoInclude** : Caminho completo para o perfil InSpec.

Executar o seguinte comando para criar um pacote utilizando a configuração dada no passo anterior:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'wmi_service' `
  -Configuration './Config/wmi_service.mof' `
  -FilesToInclude './wmi_service'  `
  -Path './package' 
```

## <a name="policy-lifecycle"></a>Ciclo de vida da política

Se quiser lançar uma atualização da política, existem dois campos que requerem atenção.

- **Versão** : Quando executar o `New-GuestConfigurationPolicy` cmdlet, deve especificar um número de versão maior do que o que é publicado atualmente. A propriedade atualiza a versão da atribuição de Configuração de Hóspedes para que o agente reconheça o pacote atualizado.
- **contentHash** : Esta propriedade é atualizada automaticamente pelo `New-GuestConfigurationPolicy` cmdlet. É um valor haxixe do pacote criado `New-GuestConfigurationPackage` por. A propriedade deve estar correta para o `.zip` ficheiro que publica. Se apenas a propriedade **contentUri** for atualizada, a Extensão não aceitará o pacote de conteúdo.

A forma mais fácil de lançar um pacote atualizado é repetir o processo descrito neste artigo e fornecer um número de versão atualizado. Este processo garante que todas as propriedades foram corretamente atualizadas.

## <a name="optional-signing-guest-configuration-packages"></a>Opcional: Assinar pacotes de configuração de hóspedes

As políticas personalizadas de Configuração do Hóspede usam hash SHA256 para validar o pacote de política não mudou.
Opcionalmente, os clientes também podem usar um certificado para assinar pacotes e forçar a extensão de Configuração do Hóspede apenas para permitir conteúdo assinado.

Para ativar este cenário, há dois passos que precisa de completar. Executar o cmdlet para assinar o pacote de conteúdo, e anexar uma etiqueta às máquinas que devem exigir a assinatura do código.

Para utilizar a função Validação de Assinatura, execute o `Protect-GuestConfigurationPackage` cmdlet para assinar a embalagem antes de ser publicada. Este cmdlet requer um certificado de 'Assinatura de Código'.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

Parâmetros do `Protect-GuestConfigurationPackage` cmdlet:

- **Caminho** : Percurso completo do pacote de Configuração de Convidados.
- **Certificado** : Certificado de assinatura de código para assinar o pacote. Este parâmetro só é suportado ao assinar conteúdo para o Windows.

O agente de configuração de hóspedes espera que a chave pública do certificado esteja presente em "Autoridades de Certificados de Raiz Fidedignas" em máquinas Windows e no caminho `/usr/local/share/ca-certificates/extra` nas máquinas Linux. Para que o nó verifique o conteúdo assinado, instale a chave pública do certificado na máquina antes de aplicar a política personalizada. Este processo pode ser feito utilizando qualquer técnica dentro do VM ou utilizando a Política Azure. Um modelo de exemplo é [fornecido aqui.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)
A política de acesso ao Cofre-Chave deve permitir ao fornecedor de recursos compute aceder a certificados durante as implementações. Para obter etapas detalhadas, consulte [Configurar o Cofre de Chaves para máquinas virtuais no Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Segue-se um exemplo para exportar a chave pública de um certificado de assinatura, para importar para a máquina.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

Após a publicação do seu conteúdo, apedie uma etiqueta com nome `GuestConfigPolicyCertificateValidation` e valor a todas as `enabled` máquinas virtuais onde deve ser necessária a assinatura de código. Consulte as [amostras tags](../samples/built-in-policies.md#tags) de como as etiquetas podem ser entregues em escala usando a Política Azure. Uma vez que esta etiqueta está em vigor, a definição de política gerada através do `New-GuestConfigurationPolicy` cmdlet permite o requisito através da extensão de Configuração do Convidado.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Atribuições de política de configuração de hóspedes de resolução de problemas (visualização)

Uma ferramenta está disponível na pré-visualização para ajudar na resolução de problemas das atribuições de Configuração de Convidados Azure Policy. A ferramenta está em pré-visualização e foi publicada na PowerShell Gallery como módulo [de Resolução de Problemas de Configuração de Convidados](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Para obter mais informações sobre os cmdlets desta ferramenta, utilize o comando Get-Help em PowerShell para mostrar a orientação incorporada. Como a ferramenta está a receber atualizações frequentes, esta é a melhor maneira de obter informações mais recentes.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a auditoria de VMs com [configuração de hóspedes.](../concepts/guest-configuration.md)
- Entenda como [criar políticas programáticas.](./programmatically-create.md)
- Saiba como [obter dados de conformidade.](./get-compliance-data.md)
