---
title: Como criar políticas de configuração de hóspedes para Windows
description: Saiba como criar uma política azure de configuração de hóspedes para windows.
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: a8231840cc20f03da44d489ae5226e7a0b4e0d48
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835959"
---
# <a name="how-to-create-guest-configuration-policies-for-windows"></a>Como criar políticas de configuração de hóspedes para Windows

Antes de criar definições políticas personalizadas, é uma boa ideia ler a informação de visão geral conceptual na página [Azure Policy Guest Configuration](../concepts/guest-configuration.md).
 
Para aprender sobre a criação de políticas de configuração de hóspedes para o Linux, consulte a página Como criar políticas de [Configuração de Hóspedes para o Linux](./guest-configuration-create-linux.md)

Ao auditar o Windows, a Configuração do Hóspede utiliza um módulo de recurso [de configuração do Estado Desejado](/powershell/scripting/dsc/overview/overview) (DSC) para criar o ficheiro de configuração. A configuração DSC define a condição em que a máquina deve estar.
Se a avaliação da configuração falhar, o efeito de política **auditaIfNotExists** é desencadeado e a máquina é considerada **incompatível**.

[A configuração](../concepts/guest-configuration.md) do hóspede da Política Azure só pode ser usada para auditar definições dentro das máquinas. A reparação das definições dentro das máquinas ainda não está disponível.

Utilize as seguintes ações para criar a sua própria configuração para validar o estado de uma máquina Azure ou não Azure.

> [!IMPORTANT]
> As políticas personalizadas com configuração do hóspede são uma funcionalidade de pré-visualização.
>
> A extensão de Configuração de Hóspedes é necessária para realizar auditorias em máquinas virtuais Azure.
> Para implementar a extensão em escala em todas as máquinas do Windows, atribua as seguintes definições políticas:
>   - [Implemente pré-requisitos para ativar a Política de Configuração do Hóspede nos VMs do Windows.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)

## <a name="install-the-powershell-module"></a>Instale o módulo PowerShell

O módulo de Configuração de Hóspedes automatiza o processo de criação de conteúdo personalizado, incluindo:

- Criação de um artefacto de conteúdo de configuração de hóspedes (.zip)
- Teste automatizado do artefacto
- Criar uma definição política
- Publicação da política

O módulo pode ser instalado numa máquina que executa Windows, macOS ou Linux com PowerShell 6.2 ou mais tarde a funcionar localmente, ou com [a Azure Cloud Shell,](https://shell.azure.com)ou com a [imagem Azure PowerShell Core Docker](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> A compilação de configurações ainda não é suportada no Linux.

### <a name="base-requirements"></a>Requisitos de base

Sistemas operativos onde o módulo pode ser instalado:

- Linux
- macOS
- Windows

O módulo de recursos de configuração do hóspede requer o seguinte software:

- PowerShell 6.2 ou mais tarde. Se não estiver ainda instalado, siga [estas instruções](/powershell/scripting/install/installing-powershell).
- Azure PowerShell 1.5.0 ou superior. Se não estiver ainda instalado, siga [estas instruções](/powershell/azure/install-az-ps).
  - São necessários apenas os módulos AZ 'Az.Accounts' e 'Az.Resources'.

### <a name="install-the-module"></a>Instalar o módulo

Para instalar o módulo **GuestConfiguration** no PowerShell:

1. A partir de um pedido powerShell, executar o seguinte comando:

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. Validar que o módulo foi importado:

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="guest-configuration-artifacts-and-policy-for-windows"></a>Artefactos e políticas de configuração de hóspedes para Windows

A Configuração do Hóspede utiliza a Configuração do Estado Desejada powerShell como uma abstração de linguagem para escrever o que auditar no Windows. O agente carrega uma instância autónoma de PowerShell 6.2, pelo que não existe conflito com o uso do PowerShell DSC no Windows PowerShell 5.1, e não há necessidade de pré-instalar o PowerShell 6.2 ou mais tarde.

Para uma visão geral dos conceitos dSC e terminologia, consulte [powerShell DSC visão geral](/powershell/scripting/dsc/overview/overview).

### <a name="how-guest-configuration-modules-differ-from-windows-powershell-dsc-modules"></a>Como os módulos de configuração do hóspede diferem dos módulos DSC do Windows PowerShell

Quando a Configuração do Hóspede audita uma máquina:

1. O agente corre primeiro `Test-TargetResource` para determinar se a configuração está no estado correto.
1. O valor booleano devolvido pela função determina se o estatuto de Gestor de Recursos Azure para a Atribuição de Hóspedes deve ser conforme/não conforme.
1. O fornecedor corre para devolver o estado atual de cada configuração para que os detalhes estejam disponíveis tanto sobre o porquê de `Get-TargetResource` uma máquina não estar em conformidade e confirmar que o estado atual está em conformidade.

### <a name="get-targetresource-requirements"></a>Requisitos de Get-TargetResource

A função `Get-TargetResource` tem requisitos especiais para configuração do hóspede que não foram necessários para a Configuração do Estado Desejado pelo Windows.

- O hashtable que é devolvido deve incluir uma propriedade chamada **Reasons**.
- A propriedade Reasons deve ser uma matriz.
- Cada item da matriz deve ser um hashtable com teclas chamadas **Código** e **Frase**.

A propriedade Reasons é usada pelo serviço para normalizar a forma como a informação é apresentada quando uma máquina está fora de conformidade. Pode pensar em cada item em Reasons como uma "razão" para que o recurso não seja compatível. A propriedade é uma matriz porque um recurso pode estar fora de conformidade por mais de uma razão.

As propriedades **Código** e **Frase** são esperadas pelo serviço. Ao autorizar um recurso personalizado, detete o texto (tipicamente stdout) que gostaria de mostrar como a razão pela qual o recurso não é compatível como o valor para **frase**. **O Código** tem requisitos específicos de formatação, pelo que o relatório pode mostrar claramente informações sobre o recurso utilizado para fazer a auditoria. Esta solução torna a Configuração do Hóspede extensível. Qualquer comando pode ser executado desde que a saída possa ser devolvida como um valor de cadeia para a propriedade **Frase.**

- **Código** (cadeia): O nome do recurso, repetido, e depois um nome curto sem espaços como identificador pela razão. Estes três valores devem ser delimitados pelo cólon sem espaços.
  - Um exemplo seria`registry:registry:keynotpresent`
- **Frase** (corda): Texto legível pelo homem para explicar por que a definição não é compatível.
  - Um exemplo seria`The registry key $key is not present on the machine.`

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

A propriedade Reasons também deve ser adicionada ao esquema MOF para o recurso como uma classe incorporada.

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

O nome da configuração personalizada deve ser consistente em todo o lado. O nome do ficheiro .zip para o pacote de conteúdo, o nome de configuração no ficheiro MOF e o nome de atribuição de hóspedes no modelo de Gestor de Recursos, devem ser os mesmos.

### <a name="scaffolding-a-guest-configuration-project"></a>Andaimes um projeto de configuração de hóspedes

Os desenvolvedores que gostariam de acelerar o processo de começar e trabalhar a partir do código de amostra podem instalar um projeto comunitário chamado Projeto de **Configuração de Hóspedes**. O projeto instala um modelo para o módulo [PowerShell de gesso.](https://github.com/powershell/plaster) Esta ferramenta pode ser usada para andaimes um projeto, incluindo uma configuração de trabalho e recurso de amostra, e um conjunto de testes [pester](https://github.com/pester/pester) para validar o projeto. O modelo também inclui task runners para Visual Studio Code para automatizar a construção e validação do pacote de Configuração de Hóspedes. Para mais informações, consulte o projeto GitHub Projeto de [Configuração de Hóspedes](https://github.com/microsoft/guestconfigurationproject).

Para obter mais informações sobre o trabalho com configurações em geral, consulte [Escrever, Compilar e Aplicar uma Configuração](/powershell/scripting/dsc/configurations/write-compile-apply-configuration).

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>Conteúdo esperado de um artefacto de configuração de hóspedes

O pacote completo é utilizado pela Configuração do Hóspede para criar as definições de Política Azure. O pacote consiste em:

- A configuração dSC compilada como um MOF
- Pasta de módulos
  - Módulo de Configuração de Hóspedes
  - Módulo DscNativeResources
  - (Janelas) Módulos de recursos DSC exigidos pela MOF

Os cmdlets PowerShell ajudam na criação do pacote.
Não é necessária pasta ou pasta de versão de nível raiz.
O formato do pacote deve ser um ficheiro .zip.

### <a name="storing-guest-configuration-artifacts"></a>Armazenar artefactos de configuração de hóspedes

O pacote .zip deve ser armazenado num local acessível pelas máquinas virtuais geridas.
Exemplos incluem repositórios GitHub, um Armazém Azure Repo ou Azure. Se preferir não tornar o pacote público, pode incluir um [token SAS](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md) no URL.
Também poderia implementar [o ponto final](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) de serviço para máquinas numa rede privada, embora esta configuração se aplique apenas ao acesso ao pacote e não comunicação com o serviço.

## <a name="step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows"></a>Passo a passo, criando uma política de auditoria personalizada de Configuração de Hóspedes para windows

Crie uma configuração DSC para auditar definições. O seguinte exemplo de script PowerShell cria uma configuração chamada **AuditBitLocker,** importa o módulo de recursos **psDscResources,** e usa o `Service` recurso para auditar para um serviço de execução. O script de configuração pode ser executado a partir de uma máquina Windows ou macOS.

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

Guarde este ficheiro com o nome `config.ps1` na pasta do projeto. Execute-o na PowerShell executando `./config.ps1` no terminal. Um novo ficheiro mof é criado.

O `Node AuditBitlocker` comando não é tecnicamente necessário, mas produz um ficheiro nomeado em vez do `AuditBitlocker.mof` `localhost.mof` padrão. Ter o nome de ficheiro .mof seguir a configuração torna fácil organizar muitos ficheiros ao operar em escala.

Uma vez compilado o MOF, os ficheiros de suporte devem ser embalados em conjunto. O pacote completo é utilizado pela Configuração do Hóspede para criar as definições de Política Azure.

O `New-GuestConfigurationPackage` cmdlet cria a embalagem. Os módulos necessários pela configuração devem estar disponíveis em `$Env:PSModulePath` . Parâmetros do `New-GuestConfigurationPackage` cmdlet ao criar conteúdo windows:

- **Nome**: Nome do pacote de configuração do hóspede.
- **Configuração**: Documento de configuração DSC compilado caminho completo.
- **Caminho**: Caminho da pasta de saída. Este parâmetro é opcional. Se não especificado, o pacote é criado no diretório atual.

Executar o seguinte comando para criar uma embalagem utilizando a configuração dada no passo anterior:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditBitlocker' `
  -Configuration './Config/AuditBitlocker.mof'
```

Depois de criar o pacote de configuração, mas antes de publicá-lo no Azure, pode testar o pacote a partir da sua estação de trabalho ou ambiente CI/CD. O cmdlet GuestConfiguration inclui o mesmo agente no seu ambiente de desenvolvimento que é usado dentro das `Test-GuestConfigurationPackage` máquinas Azure. Utilizando esta solução, pode fazer testes de integração localmente antes de lançar para ambientes de nuvem faturada.

Uma vez que o agente está realmente a avaliar o ambiente local, na maioria dos casos é preciso executar o Teste-cmdlet na mesma plataforma de SO que planeia auditar. O teste utiliza apenas módulos incluídos no pacote de conteúdos.

Parâmetros do `Test-GuestConfigurationPackage` cmdlet:

- **Nome**: Nome da política de configuração do hóspede.
- **Parâmetro**: Parâmetros de política fornecidos em formato hashtable.
- **Caminho**: Caminho completo do pacote de configuração de hóspedes.

Executar o seguinte comando para testar a embalagem criada pelo passo anterior:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditBitlocker.zip
```

O cmdlet também suporta a entrada do gasoduto PowerShell. Tubo a saída de `New-GuestConfigurationPackage` cmdlet para o `Test-GuestConfigurationPackage` cmdlet.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditBitlocker -Configuration ./Config/AuditBitlocker.mof | Test-GuestConfigurationPackage
```

O próximo passo é publicar o ficheiro no armazenamento de blob. O script abaixo contém uma função que pode utilizar para automatizar esta tarefa. Os comandos utilizados na `publish` função requerem o `Az.Storage` módulo.

```azurepowershell-interactive
function publish {
    param(
    [Parameter(Mandatory=$true)]
    $resourceGroup,
    [Parameter(Mandatory=$true)]
    $storageAccountName,
    [Parameter(Mandatory=$true)]
    $storageContainerName,
    [Parameter(Mandatory=$true)]
    $filePath,
    [Parameter(Mandatory=$true)]
    $blobName
    )

    # Get Storage Context
    $Context = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
        -Name $storageAccountName | `
        ForEach-Object { $_.Context }

    # Upload file
    $Blob = Set-AzStorageBlobContent -Context $Context `
        -Container $storageContainerName `
        -File $filePath `
        -Blob $blobName `
        -Force

    # Get url with SAS token
    $StartTime = (Get-Date)
    $ExpiryTime = $StartTime.AddYears('3')  # THREE YEAR EXPIRATION
    $SAS = New-AzStorageBlobSASToken -Context $Context `
        -Container $storageContainerName `
        -Blob $blobName `
        -StartTime $StartTime `
        -ExpiryTime $ExpiryTime `
        -Permission rl `
        -FullUri

    # Output
    return $SAS
}

# replace the $storageAccountName value below, it must be globally unique
$resourceGroup        = 'policyfiles'
$storageAccountName   = 'youraccountname'
$storageContainerName = 'artifacts'

$uri = publish `
  -resourceGroup $resourceGroup `
  -storageAccountName $storageAccountName `
  -storageContainerName $storageContainerName `
  -filePath ./AuditBitlocker.zip `
  -blobName 'AuditBitlocker'
```

Uma vez criado e carregado um pacote de política personalizada de configuração de hóspedes, crie a definição de política de configuração do hóspede. O `New-GuestConfigurationPolicy` cmdlet pega num pacote de política personalizado e cria uma definição de política.

Parâmetros do `New-GuestConfigurationPolicy` cmdlet:

- **ContentUri**: Public http(s) uri do pacote de conteúdo de configuração de hóspedes.
- **Nome do ecrã**: Nome do exibição de política.
- **Descrição**: Descrição da política.
- **Parâmetro**: Parâmetros de política fornecidos em formato hashtable.
- **Versão**: Versão política.
- **Caminho**: Caminho de destino onde são criadas definições políticas.
- **Plataforma**: Plataforma alvo (Windows/Linux) para política de configuração de hóspedes e pacote de conteúdo.

O exemplo seguinte cria as definições de política num caminho especificado a partir de um pacote de política personalizada:

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

- **auditIfNotExists.json**
- **implementarIfNotExists.json**
- **Iniciativa.json**

A saída de cmdlet devolve um objeto que contém o nome e o caminho da exibição da iniciativa e do caminho dos ficheiros de política.

> [!Note]
> O mais recente módulo de Configuração de Hóspedes inclui novos parâmetros:
> - **Tag** adiciona um ou mais filtros de etiqueta à definição de política
>   - Consulte a secção Filtrar as políticas de [configuração dos hóspedes utilizando tags](#filtering-guest-configuration-policies-using-tags).
> - **Categoria** define o campo de metadados de categoria na definição de política
>   - Se o parâmetro não estiver incluído, a categoria não se aplica à Configuração do Hóspede.
> Estas funcionalidades estão em pré-visualização e requerem a versão 1.20.1 do módulo de configuração do hóspede, que pode ser instalada utilizando `Install-Module GuestConfiguration -AllowPrerelease` .

Por fim, publique as definições políticas utilizando o `Publish-GuestConfigurationPolicy` cmdlet. O cmdlet tem apenas o parâmetro **Caminho** que aponta para a localização dos ficheiros JSON criados por `New-GuestConfigurationPolicy` .

Para executar o comando Publish, precisa de acesso para criar políticas no Azure. Os requisitos específicos de autorização estão documentados na página de visão geral da [política do Azure.](../overview.md) O melhor papel incorporado é o Contribuinte da Política de **Recursos.**

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions'
```

O `Publish-GuestConfigurationPolicy` cmdlet aceita o caminho do gasoduto PowerShell. Esta funcionalidade significa que pode criar os ficheiros de política e publicá-los num único conjunto de comandos canalizados.

```azurepowershell-interactive
New-GuestConfigurationPolicy `
 -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit BitLocker service.' `
  -Description 'Audit if the BitLocker service is not enabled on Windows machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
```

Com a política criada no Azure, o último passo é atribuir a iniciativa. Veja como atribuir a iniciativa ao [Portal,](../assign-policy-portal.md) [Azure CLI](../assign-policy-azurecli.md)e [Azure PowerShell.](../assign-policy-powershell.md)

> [!IMPORTANT]
> As políticas de Configuração de Hóspedes devem ser **sempre** atribuídas utilizando a iniciativa que combina as políticas _AuditIfNotExists_ e _DeployIfNotExists._ Se apenas for atribuída a política _AuditIfNotExists,_ os pré-requisitos não são implementados e a política mostra sempre que os servidores '0' estão em conformidade.

Atribuir uma definição de política com efeito _DeployIfNotExists_ requer um nível adicional de acesso. Para conceder o menor privilégio, pode criar uma definição de papel personalizada que alarga o Colaborador da Política de **Recursos.** O exemplo abaixo cria uma função chamada **Colaborador de Política** de Recursos DINE com a permissão adicional _Microsoft.Authorization/roleAssignments/write_.

```azurepowershell-interactive
$subscriptionid = '00000000-0000-0000-0000-000000000000'
$role = Get-AzRoleDefinition "Resource Policy Contributor"
$role.Id = $null
$role.Name = "Resource Policy Contributor DINE"
$role.Description = "Can assign Policies that require remediation."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Authorization/roleAssignments/write")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/$subscriptionid")
New-AzRoleDefinition -Role $role
```

### <a name="filtering-guest-configuration-policies-using-tags"></a>Filtrar as políticas de configuração dos hóspedes usando tags

> [!Note]
> Esta funcionalidade encontra-se em pré-visualização e requer a versão 1.20.1 do módulo de configuração do hóspede, que pode ser instalada utilizando `Install-Module GuestConfiguration -AllowPrerelease` .

As definições de política criadas por cmdlets no módulo de Configuração de Hóspedes podem incluir opcionalmente um filtro para etiquetas. O parâmetro **tag** suporta `New-GuestConfigurationPolicy` uma variedade de hashtables contendo inteiros de etiquetas individuais. As etiquetas são adicionadas à `If` secção da definição de política e não podem ser modificadas por uma atribuição de políticas.

Um exemplo de uma definição de política que filtra as etiquetas é dada abaixo.

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

### <a name="using-parameters-in-custom-guest-configuration-policy-definitions"></a>Utilização de parâmetros nas definições de política personalizadas de configuração do hóspede

A Configuração do Hóspede suporta propriedades dominantes de uma Configuração no tempo de execução. Esta funcionalidade significa que os valores do ficheiro MOF no pacote não têm de ser considerados estáticos. Os valores de sobreposição são fornecidos através da Política Azure e não têm impacto na forma como as Configurações são autoras ou compiladas.

Os cmdlets `New-GuestConfigurationPolicy` e `Test-GuestConfigurationPolicyPackage` incluem um parâmetro chamado **Parâmetros**. Este parâmetro tem uma definição hashtable, incluindo todos os detalhes sobre cada parâmetro e cria as secções necessárias de cada ficheiro utilizado para a definição de Política Azure.

O exemplo seguinte cria uma definição de política para auditar um serviço, onde o utilizador seleciona a partir de uma lista no momento da atribuição de políticas.

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'ServiceName'                                            # Policy parameter name (mandatory)
        DisplayName = 'windows service name.'                           # Policy parameter display name (mandatory)
        Description = "Name of the windows service to be audited."      # Policy parameter description (optional)
        ResourceType = "Service"                                        # DSC configuration resource type (mandatory)
        ResourceId = 'windowsService'                                   # DSC configuration resource property name (mandatory)
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
    -Parameters $PolicyParameterInfo `
    -Version 1.0.0
```

## <a name="extending-guest-configuration-with-third-party-tools"></a>Alargar a Configuração do Hóspede com ferramentas de terceiros

> [!Note]
> Esta funcionalidade encontra-se em pré-visualização e requer a versão 1.20.1 do módulo de configuração do hóspede, que pode ser instalada utilizando `Install-Module GuestConfiguration -AllowPrerelease` .
> Na versão 1.20.1, esta funcionalidade só está disponível para definições de políticas que auditam máquinas Windows

Os pacotes de artefactos para configuração de hóspedes podem ser estendidos para incluir ferramentas de terceiros.
O alargamento da Configuração do Hóspede requer o desenvolvimento de dois componentes.

- Um recurso de Configuração do Estado desejado que lida com toda a atividade relacionada com a gestão da ferramenta de terceiros
  - Instalar
  - Invocar
  - Converter a produção
- Conteúdo no formato correto para a ferramenta consumir de forma nativa

O recurso DSC requer desenvolvimento personalizado se uma solução comunitária já não existir.
As soluções comunitárias podem ser descobertas pesquisando na PowerShell Gallery para tag [GuestConfiguration](https://www.powershellgallery.com/packages?q=Tags%3A%22GuestConfiguration%22).

> [!Note]
> A extensibility de Configuração de Hóspedes é um cenário de "trazer a sua própria licença". Certifique-se de que cumpriu os termos e condições de quaisquer ferramentas de terceiros antes da utilização.

Depois de o recurso DSC ter sido instalado no ambiente de desenvolvimento, utilize o parâmetro **FilesToInclude** para `New-GuestConfigurationPackage` incluir conteúdo para a plataforma de terceiros no artefacto de conteúdo.

### <a name="step-by-step-creating-a-content-artifact-that-uses-third-party-tools"></a>Passo a passo, criando um artefacto de conteúdo que usa ferramentas de terceiros

Apenas o `New-GuestConfigurationPackage` cmdlet requer uma alteração da orientação passo a passo para os artefactos de conteúdo dSC. Para este exemplo, utilize o `gcInSpec` módulo para estender a Configuração do Hóspede para auditar as máquinas Windows utilizando a plataforma InSpec em vez do módulo incorporado utilizado no Linux. O módulo comunitário é mantido como um [projeto de código aberto no GitHub.](https://github.com/microsoft/gcinspec)

Instale módulos necessários no seu ambiente de desenvolvimento:

```azurepowershell-interactive
Install-Module GuestConfiguration, gcInSpec
```

Em primeiro lugar, crie o ficheiro YaML utilizado pelo InSpec. O ficheiro fornece informações básicas sobre o ambiente. Um exemplo é dado abaixo:

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

Guarde este ficheiro para uma pasta nomeada no seu diretório de `wmi_service` projeto.

Em seguida, crie o ficheiro Ruby com a abstração da linguagem InSpec usada para auditar a máquina.

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

Guarde este ficheiro numa nova pasta chamada `controls` dentro do `wmi_service` diretório.

Por fim, crie uma configuração, importe o módulo de recursos **GuestConfiguration** e utilize o `gcInSpec` recurso para definir o nome do perfil InSpec.

```powershell
# Define the configuration and import GuestConfiguration
Configuration wmi_service
{
    Import-DSCResource -Module @{ModuleName = 'gcInSpec'; ModuleVersion = '2.0.0'}
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

O `New-GuestConfigurationPackage` cmdlet cria a embalagem. Para conteúdos de terceiros, utilize o parâmetro **FilesToInclude** para adicionar o conteúdo InSpec à embalagem. Você não precisa especificar o **ChefProfilePath** quanto aos pacotes Linux.

- **Nome**: Nome do pacote de configuração do hóspede.
- **Configuração**: Conjunto completo do documento de configuração compilado.
- **Caminho**: Caminho da pasta de saída. Este parâmetro é opcional. Se não especificado, o pacote é criado no diretório atual.
- **FilesoInclude**: Caminho completo para o perfil InSpec.

Executar o seguinte comando para criar uma embalagem utilizando a configuração dada no passo anterior:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'wmi_service' `
  -Configuration './Config/wmi_service.mof' `
  -FilesToInclude './wmi_service'
```

## <a name="policy-lifecycle"></a>Ciclo de vida político

Se quiser lançar uma atualização da política, há dois domínios que requerem atenção.

- **Versão**: Quando executa o `New-GuestConfigurationPolicy` cmdlet, deve especificar um número de versão maior do que o atualmente publicado. A propriedade atualiza a versão da atribuição de Configuração de Hóspedes para que o agente reconheça o pacote atualizado.
- **conteúdoHash**: Esta propriedade é atualizada automaticamente pelo `New-GuestConfigurationPolicy` cmdlet. É um valor hash do pacote criado por `New-GuestConfigurationPackage` . A propriedade deve estar correta para o `.zip` ficheiro que publica. Se apropriedade **de conteúdoUri** for atualizada, a Extensão não aceitará o pacote de conteúdo.

A forma mais fácil de lançar um pacote atualizado é repetir o processo descrito neste artigo e fornecer um número de versão atualizada. Este processo garante que todas as propriedades foram corretamente atualizadas.

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>Converter conteúdo de política do Grupo Windows para configuração de hóspedes de política azure

Configuração de hóspedes, ao auditar máquinas Windows, é uma implementação da sintaxe powerShell Desired State Configuration. A comunidade DSC publicou ferramentas para converter modelos de política de grupo exportados para formato DSC. Ao utilizar esta ferramenta juntamente com os cmdlets de Configuração de Hóspedes acima descritos, pode converter conteúdo da Política do Grupo Windows e publicá-lo para a Política do Azure para auditar. Para mais detalhes sobre a utilização da ferramenta, consulte o artigo [Quickstart: Converta](/powershell/scripting/dsc/quickstarts/gpo-quickstart)a Política de Grupo em DSC .
Uma vez convertido o conteúdo, os passos acima para criar um pacote e publicá-lo como Política Azure são os mesmos que para qualquer conteúdo DSC.

## <a name="optional-signing-guest-configuration-packages"></a>Opcional: Assinar pacotes de configuração de hóspedes

As políticas personalizadas de configuração do hóspede usam haxixe SHA256 para validar o pacote de política não mudou.
Opcionalmente, os clientes também podem usar um certificado para assinar pacotes e forçar a extensão de Configuração de Hóspedes apenas permitir conteúdo assinado.

Para permitir este cenário, há dois passos que precisa de completar. Executar o cmdlet para assinar o pacote de conteúdo e anexar uma etiqueta às máquinas que devem exigir a assinatura do código.

Para utilizar a função Signature Validação, execute o `Protect-GuestConfigurationPackage` cmdlet para assinar o pacote antes de ser publicado. Este cmdlet requer um certificado de "Assinatura de Código".

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

Parâmetros do `Protect-GuestConfigurationPackage` cmdlet:

- **Caminho**: Caminho completo do pacote de configuração de hóspedes.
- **Certificado**: Certificado de assinatura de código para assinar a embalagem. Este parâmetro só é suportado ao assinar conteúdo para windows.

O agente GuestConfiguration espera que a chave pública do certificado esteja presente nas "Autoridades de Certificados de Raiz Fidedignas" nas máquinas Windows e no caminho para as `/usr/local/share/ca-certificates/extra` máquinas Linux. Para que o nó verifique o conteúdo assinado, instale a chave pública do certificado na máquina antes de aplicar a política personalizada. Este processo pode ser feito utilizando qualquer técnica dentro do VM ou utilizando a Política Azure. Um modelo de exemplo é [fornecido aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
A política de acesso key vault deve permitir ao fornecedor de recursos Compute aceder a certificados durante as implementações. Para obter passos detalhados, consulte [Abóbada chave para máquinas virtuais em Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Segue-se um exemplo para exportar a chave pública de um certificado de assinatura para importar para a máquina.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

Após a publicação do seu conteúdo, acomode uma etiqueta com nome `GuestConfigPolicyCertificateValidation` e valor a todas as máquinas virtuais onde deve ser necessária a assinatura de `enabled` código. Consulte as [amostras de Etiqueta](../samples/built-in-policies.md#tags) para saber como as etiquetas podem ser entregues em escala utilizando a Política Azure. Uma vez que esta etiqueta esteja em vigor, a definição de política gerada usando o `New-GuestConfigurationPolicy` cmdlet permite o requisito através da extensão de Configuração do Hóspede.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Tarefas de política de configuração de hóspedes de resolução de problemas (pré-visualização)

Uma ferramenta está disponível em pré-visualização para ajudar na resolução de tarefas de configuração de hóspedes da Política Azure. A ferramenta está em pré-visualização e foi publicada na PowerShell Gallery como nome de módulo [Guest Configuration Troubleshooter](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Para obter mais informações sobre os cmdlets nesta ferramenta, utilize o comando Get-Help na PowerShell para mostrar a orientação incorporada. Como a ferramenta está a receber atualizações frequentes, essa é a melhor maneira de obter informações mais recentes.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre auditoria de VMs com [Configuração de Hóspedes](../concepts/guest-configuration.md).
- Compreender como [criar políticas programáticas.](programmatically-create.md)
- Saiba como obter dados de [conformidade.](get-compliance-data.md)
