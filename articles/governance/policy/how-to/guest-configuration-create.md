---
title: Como criar políticas de configuração de convidado
description: Saiba como criar uma política de configuração de convidado Azure Policy para VMs Windows ou Linux com Azure PowerShell.
ms.date: 11/21/2019
ms.topic: conceptual
ms.openlocfilehash: 2e653d07e783425afdcd71f9d58e3569692faaf9
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74407044"
---
# <a name="how-to-create-guest-configuration-policies"></a>Como criar políticas de configuração de convidado

A configuração de convidado usa um módulo de recurso de [configuração de estado desejado](/powershell/scripting/dsc/overview/overview) (DSC) para criar a configuração de auditoria dos computadores do Azure. A configuração DSC define a condição em que o computador deve estar. Se a avaliação da configuração falhar, o efeito de política **auditIfNotExists** será disparado e o computador será considerado **não compatível**.

[Azure Policy configuração de convidado](../concepts/guest-configuration.md) só pode ser usada para auditar configurações dentro de computadores. A correção de configurações dentro de computadores ainda não está disponível.

Use as ações a seguir para criar sua própria configuração para validar o estado de um computador do Azure.

> [!IMPORTANT]
> As políticas personalizadas com a configuração de convidado são um recurso de visualização.

## <a name="add-the-guestconfiguration-resource-module"></a>Adicionar o módulo de recurso GuestConfiguration

Para criar uma política de configuração de convidado, o módulo de recurso deve ser adicionado. Este módulo de recurso pode ser usado com o PowerShell instalado localmente, com [Azure cloud Shell](https://shell.azure.com)ou com a [imagem de Azure PowerShell Core do Docker](https://hub.docker.com/r/azuresdk/azure-powershell-core).

### <a name="base-requirements"></a>Requisitos de base

O módulo de recurso de configuração de convidado requer o seguinte software:

- PowerShell. Se não estiver ainda instalado, siga [estas instruções](/powershell/scripting/install/installing-powershell).
- Azure PowerShell 1.5.0 ou superior. Se não estiver ainda instalado, siga [estas instruções](/powershell/azure/install-az-ps).

### <a name="install-the-module"></a>Instalar o módulo

A configuração de convidado usa o módulo de recurso **GuestConfiguration** para criar configurações DSC e publicá-las no Azure Policy:

1. Em um prompt do PowerShell, execute o seguinte comando:

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. Valide se o módulo foi importado:

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="create-custom-guest-configuration-configuration-and-resources"></a>Criar recursos e configuração de configuração de convidado personalizada

A primeira etapa para criar uma política personalizada para a configuração de convidado é criar a configuração DSC. Para obter uma visão geral dos conceitos e da terminologia do DSC, consulte [visão geral do DSC do PowerShell](/powershell/scripting/dsc/overview/overview).

Se sua configuração exigir apenas recursos que são internos com a instalação do agente de configuração convidada, você só precisará criar um arquivo MOF de configuração. Se precisar executar um script adicional, você precisará criar um módulo de recurso personalizado.

### <a name="requirements-for-guest-configuration-custom-resources"></a>Requisitos para recursos personalizados de configuração de convidado

Quando a configuração de convidado audita um computador, ele é executado pela primeira vez `Test-TargetResource` para determinar se ele está no estado correto. O valor booliano retornado pela função determina se o status de Azure Resource Manager para a atribuição de convidado deve ser compatível/não em conformidade. Se o booliano for `$false` para qualquer recurso na configuração, o provedor será executado `Get-TargetResource`. Se o booliano for `$true`, `Get-TargetResource` não será chamado.

A função `Get-TargetResource` tem requisitos especiais para configuração de convidado que não foram necessários para a configuração de estado desejado do Windows.

- A tabela de hash retornada deve incluir uma propriedade chamada **Reasons**.
- A propriedade Reasons deve ser uma matriz.
- Cada item na matriz deve ser uma tabela de hash com chaves denominadas **Code** e **frase**.

A propriedade Reasons é usada pelo serviço para padronizar a forma como as informações são apresentadas quando um computador está fora de conformidade. Você pode considerar cada item por motivos como um "motivo" pelo qual o recurso não está em conformidade. A propriedade é uma matriz porque um recurso pode estar fora de conformidade por mais de um motivo.

O **código** e a **frase** das propriedades são esperados pelo serviço. Ao criar um recurso personalizado, defina o texto (normalmente stdout) que você gostaria de mostrar como o motivo pelo qual o recurso não está em conformidade como o valor de **frase**. O **código** tem requisitos de formatação específicos para que os relatórios possam exibir claramente informações sobre o recurso que foi usado para executar a auditoria. Essa solução torna a configuração de convidado extensível. Qualquer comando pode ser executado para auditar um computador, contanto que a saída possa ser capturada e retornada como um valor de cadeia de caracteres para a propriedade de **frase** .

- **Code** (String): o nome do recurso, repetido e, em seguida, um nome curto sem espaços como um identificador para o motivo. Esses três valores devem ser delimitados por dois-pontos sem espaços.
  - Um exemplo seria `registry:registry:keynotpresent`
- **Frase** (cadeia de caracteres): texto legível por humanos para explicar por que a configuração não está em conformidade.
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

#### <a name="scaffolding-a-guest-configuration-project"></a>Scaffolding um projeto de configuração de convidado

Para os desenvolvedores que desejarem acelerar o processo de introdução e trabalho a partir do código de exemplo, um projeto de comunidade chamado **projeto de configuração de convidado** existe como um modelo para o módulo [Plaster](https://github.com/powershell/plaster) do PowerShell. Essa ferramenta pode ser usada para Scaffold um projeto, incluindo uma configuração de trabalho e um recurso de exemplo, e um conjunto de testes de [Pester](https://github.com/pester/pester) para validar o projeto. O modelo também inclui executores de tarefas para Visual Studio Code para automatizar a criação e a validação do pacote de configuração do convidado. Para obter mais informações, consulte o [projeto de configuração de convidado](https://github.com/microsoft/guestconfigurationproject)do projeto do github.

### <a name="custom-guest-configuration-configuration-on-linux"></a>Configuração de convidado personalizada no Linux

A configuração de DSC para configuração de convidado no Linux usa o recurso de `ChefInSpecResource` para fornecer ao mecanismo o nome da definição de [inspec do chefe](https://www.chef.io/inspec/) . **Nome** é a única propriedade de recurso necessária.

O exemplo a seguir cria uma configuração denominada **linha de base**, importa o módulo de recurso **GuestConfiguration** e usa o recurso `ChefInSpecResource` definir o nome da definição inspec como **Linux-patch-Baseline**:

```azurepowershell-interactive
# Define the DSC configuration and import GuestConfiguration
Configuration baseline
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    ChefInSpecResource 'Audit Linux patch baseline'
    {
        Name = 'linux-patch-baseline'
    }
}

# Compile the configuration to create the MOF files
baseline
```

Para obter mais informações, consulte [gravar, compilar e aplicar uma configuração](/powershell/scripting/dsc/configurations/write-compile-apply-configuration).

### <a name="custom-guest-configuration-configuration-on-windows"></a>Configuração de convidado personalizada no Windows

A configuração de DSC para Azure Policy configuração de convidado é usada pelo agente de configuração de convidado somente; ela não entra em conflito com a configuração de estado desejado do Windows PowerShell.

O exemplo a seguir cria uma configuração chamada **AuditBitLocker**, importa o módulo de recurso **GuestConfiguration** e usa o recurso `Service` para auditar um serviço em execução:

```azurepowershell-interactive
# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Service 'Ensure BitLocker service is present and running'
    {
        Name = 'BDESVC'
        Ensure = 'Present'
        State = 'Running'
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker
```

Para obter mais informações, consulte [gravar, compilar e aplicar uma configuração](/powershell/scripting/dsc/configurations/write-compile-apply-configuration).

## <a name="create-guest-configuration-custom-policy-package"></a>Criar pacote de política personalizada de configuração de convidado

Depois que o MOF é compilado, os arquivos de suporte devem ser empacotados juntos. O pacote concluído é usado pela configuração de convidado para criar as definições de Azure Policy. O pacote consiste em:

- A configuração de DSC compilada como um MOF
- Pasta modules
  - Módulo GuestConfiguration
  - Módulo DscNativeResources
  - Linux Uma pasta com a definição do chefe inspec e conteúdo adicional
  - Windows Módulos de recursos de DSC que não são internos

O cmdlet `New-GuestConfigurationPackage` cria o pacote. O seguinte formato é usado para criar um pacote personalizado:

```azurepowershell-interactive
New-GuestConfigurationPackage -Name '{PackageName}' -Configuration '{PathToMOF}' `
    -Path '{OutputFolder}' -Verbose
```

Parâmetros do cmdlet `New-GuestConfigurationPackage`:

- **Nome**: nome do pacote de configuração do convidado.
- **Configuração**: caminho completo do documento de configuração DSC compilada.
- **Caminho**: caminho da pasta de saída. Esse parâmetro é opcional. Se não for especificado, o pacote será criado no diretório atual.
- **ChefProfilePath**: caminho completo para o perfil inspec. Esse parâmetro tem suporte apenas ao criar conteúdo para auditar o Linux.

O pacote completo deve ser armazenado em um local que possa ser acessado pelas máquinas virtuais gerenciadas. Os exemplos incluem repositórios do GitHub, um repositório do Azure ou um armazenamento do Azure. Se preferir não tornar o pacote público, você pode incluir um [token SAS](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md) na URL.
Você também pode implementar o [ponto de extremidade de serviço](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) para computadores em uma rede privada, embora essa configuração se aplique somente ao acesso ao pacote e não à comunicação com o serviço.

### <a name="working-with-secrets-in-guest-configuration-packages"></a>Trabalhando com segredos em pacotes de configuração de convidado

Na configuração de convidado Azure Policy, a maneira ideal de gerenciar segredos usados em tempo de execução é armazená-los em Azure Key Vault. Esse design é implementado dentro de recursos personalizados de DSC.

1. Primeiro, crie uma identidade gerenciada atribuída pelo usuário no Azure.

   A identidade é usada por computadores para acessar segredos armazenados no Key Vault. Para obter etapas detalhadas, consulte [criar, listar ou excluir uma identidade gerenciada atribuída pelo usuário usando Azure PowerShell](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md).

1. Crie uma instância de Key Vault.

   Para obter etapas detalhadas, consulte [definir e recuperar um segredo-PowerShell](../../../key-vault/quick-create-powershell.md).
   Atribua permissões à instância para conceder ao acesso de identidade atribuído pelo usuário os segredos armazenados no Key Vault. Para obter etapas detalhadas, consulte [definir e recuperar um segredo-.net](../../../key-vault/quick-create-net.md#give-the-service-principal-access-to-your-key-vault).

1. Atribua a identidade atribuída pelo usuário ao seu computador.

   Para obter etapas detalhadas, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando o PowerShell](../../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md#user-assigned-managed-identity).
   Em escala, atribua essa identidade usando Azure Resource Manager por meio de Azure Policy. Para obter etapas detalhadas, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando um modelo](../../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md#assign-a-user-assigned-managed-identity-to-an-azure-vm).

1. Por fim, em seu recurso personalizado, use a ID do cliente gerada acima para acessar Key Vault usando o token disponível no computador.

   O `client_id` e a URL para a instância de Key Vault podem ser passados para o recurso como [Propriedades](/powershell/scripting/dsc/resources/authoringresourcemof#creating-the-mof-schema) , de modo que o recurso não precisará ser atualizado para vários ambientes ou se os valores precisarem ser alterados.

O exemplo de código a seguir pode ser usado em um recurso personalizado para recuperar segredos de Key Vault usando uma identidade atribuída pelo usuário. O valor retornado da solicitação para Key Vault é texto sem formatação. Como prática recomendada, armazene-o em um objeto de credencial.

```azurepowershell-interactive
# the following values should be input as properties
$client_id = 'e3a78c9b-4dd2-46e1-8bfa-88c0574697ce'
$keyvault_url = 'https://keyvaultname.vault.azure.net/secrets/mysecret'

$access_token = ((Invoke-WebRequest -Uri "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=$client_id&resource=https%3A%2F%2Fvault.azure.net" -Method GET -Headers @{Metadata='true'}).Content | ConvertFrom-Json).access_token

$value = ((Invoke-WebRequest -Uri $($keyvault_url+'?api-version=2016-10-01') -Method GET -Headers @{Authorization="Bearer $access_token"}).content | convertfrom-json).value |  ConvertTo-SecureString -asplaintext -force

$credential = New-Object System.Management.Automation.PSCredential('secret',$value)
```

## <a name="test-a-guest-configuration-package"></a>Testar um pacote de configuração de convidado

Depois de criar o pacote de configuração, mas antes de publicá-lo no Azure, você pode testar a funcionalidade do pacote de sua estação de trabalho ou do ambiente de CI/CD. O módulo GuestConfiguration inclui um cmdlet `Test-GuestConfigurationPackage` que carrega o mesmo agente em seu ambiente de desenvolvimento como é usado dentro de máquinas do Azure. Usando essa solução, você pode executar testes de integração localmente antes de liberar para ambientes de teste/QA/produção cobrados.

```azurepowershell-interactive
Test-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Verbose
```

Parâmetros do cmdlet `Test-GuestConfigurationPackage`:

- **Nome**: nome da política de configuração de convidado.
- **Parâmetro**: parâmetros de política fornecidos no formato de tabela de hash.
- **Caminho**: caminho completo do pacote de configuração do convidado.

O cmdlet também dá suporte à entrada do pipeline do PowerShell. Direcione a saída do cmdlet `New-GuestConfigurationPackage` para o cmdlet `Test-GuestConfigurationPackage`.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditWindowsService -Configuration .\DSCConfig\localhost.mof -Path .\package -Verbose | Test-GuestConfigurationPackage -Verbose
```

Para obter mais informações sobre como testar com parâmetros, consulte a seção abaixo [usando parâmetros em políticas de configuração de convidado personalizadas](#using-parameters-in-custom-guest-configuration-policies).

## <a name="create-the-azure-policy-definition-and-initiative-deployment-files"></a>Criar os arquivos de implantação de Azure Policy e de definição de iniciativa

Depois que um pacote de política personalizada de configuração de convidado tiver sido criado e carregado em um local acessível pelos computadores, crie a definição de política de configuração de convidado para Azure Policy. O cmdlet `New-GuestConfigurationPolicy` usa um pacote de política personalizada de configuração de convidado publicamente acessível e cria uma definição de política **auditIfNotExists** e **deployIfNotExists** . Uma definição de iniciativa de política que inclui as duas definições de política também é criada.

O exemplo a seguir cria as definições de política e iniciativa em um caminho especificado de um pacote de política personalizada de configuração de convidado para o Windows e fornece um nome, uma descrição e uma versão:

```azurepowershell-interactive
New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Platform 'Windows' `
    -Version 1.2.3.4 `
    -Verbose
```

Parâmetros do cmdlet `New-GuestConfigurationPolicy`:

- **ContentUri**: URI de http público (s) de pacote de conteúdo de configuração do convidado.
- **DisplayName**: nome de exibição da política.
- **Descrição**: Descrição da política.
- **Parâmetro**: parâmetros de política fornecidos no formato de tabela de hash.
- **Versão**: versão da política.
- **Caminho**: caminho de destino em que as definições de política são criadas.
- **Plataforma**: plataforma de destino (Windows/Linux) para a política de configuração de convidado e o pacote de conteúdo.

Os seguintes arquivos são criados por `New-GuestConfigurationPolicy`:

- **auditIfNotExists. JSON**
- **deployIfNotExists. JSON**
- **Initiative. JSON**

A saída do cmdlet retorna um objeto que contém o nome de exibição da iniciativa e o caminho dos arquivos de política.

Se você quiser usar esse comando para Scaffold um projeto de política personalizada, poderá fazer alterações nesses arquivos. Um exemplo seria modificar a seção ' If ' para avaliar se uma marca específica está presente para os computadores. Para obter detalhes sobre como criar políticas, consulte [programaticamente criar políticas](./programmatically-create.md).

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Usando parâmetros em políticas de configuração de convidado personalizadas

A configuração de convidado dá suporte à substituição de propriedades de uma configuração em tempo de execução. Esse recurso significa que os valores no arquivo MOF no pacote não precisam ser considerados estáticos. Os valores de substituição são fornecidos por meio de Azure Policy e não afetam como as configurações são criadas ou compiladas.

Os cmdlets `New-GuestConfigurationPolicy` e `Test-GuestConfigurationPolicyPackage` incluem um parâmetro denominado **Parameters**. Esse parâmetro usa uma definição de tabela de hash, incluindo todos os detalhes sobre cada parâmetro e cria automaticamente todas as seções necessárias dos arquivos usados para criar cada definição de Azure Policy.

O exemplo a seguir cria um Azure Policy para auditar um serviço, onde o usuário seleciona em uma lista de serviços no momento da atribuição de política.

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
    -Platform 'Windows' `
    -Version 1.2.3.4 `
    -Verbose
```

Para políticas do Linux, inclua a propriedade **AttributesYmlContent** em sua configuração e substitua os valores de acordo. O agente de configuração convidado cria automaticamente o arquivo YaML usado por inspec para armazenar atributos. Veja o exemplo abaixo.

```azurepowershell-interactive
Configuration FirewalldEnabled {

    Import-DscResource -ModuleName 'GuestConfiguration'

    Node FirewalldEnabled {

        ChefInSpecResource FirewalldEnabled {
            Name = 'FirewalldEnabled'
            AttributesYmlContent = "DefaultFirewalldProfile: [public]"
        }
    }
}
```

Para cada parâmetro adicional, adicione uma tabela de hash à matriz. Nos arquivos de política, você verá propriedades adicionadas ao ConfigurationName de configuração de convidado que identifica o tipo de recurso, o nome, a propriedade e o valor.

```json
{
    "apiVersion": "2018-11-20",
    "type": "Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments",
    "name": "[concat(parameters('vmName'), '/Microsoft.GuestConfiguration/', parameters('configurationName'))]",
    "location": "[parameters('location')]",
    "properties": {
        "guestConfiguration": {
            "name": "[parameters('configurationName')]",
            "version": "1.*",
            "configurationParameter": [{
                "name": "[Service]windowsService;Name",
                "value": "[parameters('ServiceName')]"
            }]
        }
    }
}
```

## <a name="publish-to-azure-policy"></a>Publicar no Azure Policy

O módulo de recurso **GuestConfiguration** oferece uma maneira de criar as definições de política e a definição de iniciativa no Azure com uma etapa por meio do cmdlet `Publish-GuestConfigurationPolicy`.
O cmdlet tem apenas o parâmetro **path** que aponta para o local dos três arquivos JSON criados por `New-GuestConfigurationPolicy`.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions' -Verbose
```

O cmdlet `Publish-GuestConfigurationPolicy` aceita o caminho do pipeline do PowerShell. Esse recurso significa que você pode criar os arquivos de política e publicá-los em um único conjunto de comandos de pipe.

```azurepowershell-interactive
New-GuestConfigurationPolicy -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' -DisplayName 'Audit BitLocker service.' -Description 'Audit if the BitLocker service is not enabled on Windows machine.' -Path '.\policyDefinitions' -Platform 'Windows' -Version 1.2.3.4 -Verbose | ForEach-Object {$_.Path} | Publish-GuestConfigurationPolicy -Verbose
```

Com as definições de política e iniciativa criadas no Azure, a última etapa é atribuir a iniciativa. Veja como atribuir a iniciativa com o [portal](../assign-policy-portal.md), [CLI do Azure](../assign-policy-azurecli.md)e [Azure PowerShell](../assign-policy-powershell.md).

> [!IMPORTANT]
> As políticas de configuração de convidado **sempre** devem ser atribuídas usando a iniciativa que combina as políticas _AuditIfNotExists_ e _DeployIfNotExists_ . Se apenas a política _AuditIfNotExists_ for atribuída, os pré-requisitos não serão implantados e a política sempre mostrará que os servidores ' 0 ' estão em conformidade.

## <a name="policy-lifecycle"></a>Ciclo de vida da política

Depois de publicar um Azure Policy personalizado usando seu pacote de conteúdo personalizado, há dois campos que devem ser atualizados se você quiser publicar uma nova versão.

- **Versão**: ao executar o cmdlet `New-GuestConfigurationPolicy`, você deve especificar um número de versão maior do que o publicado atualmente. A propriedade atualiza a versão da atribuição de configuração de convidado no novo arquivo de política, de modo que a extensão reconhecerá que o pacote foi atualizado.
- **contentHash**: essa propriedade é atualizada automaticamente pelo cmdlet `New-GuestConfigurationPolicy`. É um valor de hash do pacote criado por `New-GuestConfigurationPackage`. A propriedade deve estar correta para o arquivo de `.zip` que você publicar. Se apenas a propriedade **contentUri** for atualizada, como no caso em que alguém possa fazer uma alteração manual na definição de política do portal, a extensão não aceitará o pacote de conteúdo.

A maneira mais fácil de liberar um pacote atualizado é repetir o processo descrito neste artigo e fornecer um número de versão atualizado. Esse processo garante que todas as propriedades tenham sido corretamente atualizadas.

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>Convertendo conteúdo do Windows Política de Grupo para Azure Policy configuração de convidado

A configuração de convidado, ao auditar computadores Windows, é uma implementação da sintaxe de configuração de estado desejado do PowerShell. A comunidade de DSC publicou ferramentas para converter modelos de Política de Grupo exportados para o formato DSC. Usando essa ferramenta junto com os cmdlets de configuração de convidado descritos acima, você pode converter o conteúdo do Windows Política de Grupo e o pacote/publicá-lo para Azure Policy para auditoria. Para obter detalhes sobre como usar a ferramenta, consulte o artigo [início rápido: converter política de grupo em DSC](/powershell/scripting/dsc/quickstarts/gpo-quickstart).
Depois que o conteúdo tiver sido convertido, as etapas acima para criar um pacote e publicá-lo como Azure Policy serão as mesmas para qualquer conteúdo DSC.

## <a name="optional-signing-guest-configuration-packages"></a>OPCIONAL: assinando pacotes de configuração de convidado

Por padrão, as políticas personalizadas de configuração de convidado usam o hash SHA256 para validar que o pacote de política não mudou de quando foi publicado quando ele é lido pelo servidor que está sendo auditado.
Opcionalmente, os clientes também podem usar um certificado para assinar pacotes e forçar a extensão de configuração de convidado a permitir somente conteúdo assinado.

Para habilitar esse cenário, há duas etapas que precisam ser concluídas. Execute o cmdlet para assinar o pacote de conteúdo e acrescente uma marca para os computadores que devem exigir que o código seja assinado.

Para usar o recurso de validação de assinatura, execute o cmdlet `Protect-GuestConfigurationPackage` para assinar o pacote antes que ele seja publicado. Este cmdlet requer um certificado de ' assinatura de código '.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

Parâmetros do cmdlet `Protect-GuestConfigurationPackage`:

- **Caminho**: caminho completo do pacote de configuração do convidado.
- **Certificado**: certificado de assinatura de código para assinar o pacote. Esse parâmetro só tem suporte ao assinar conteúdo para o Windows.
- **PrivateGpgKeyPath**: caminho de chave de GPG particular. Esse parâmetro só tem suporte ao assinar conteúdo para Linux.
- **PublicGpgKeyPath**: caminho de chave de GPG pública. Esse parâmetro só tem suporte ao assinar conteúdo para Linux.

O agente GuestConfiguration espera que a chave pública do certificado esteja presente em "autoridades de certificação raiz confiáveis" em computadores Windows e no caminho `/usr/local/share/ca-certificates/extra` em computadores Linux. Para o nó verificar o conteúdo assinado, instale a chave pública do certificado no computador antes de aplicar a política personalizada. Esse processo pode ser feito usando qualquer técnica dentro da VM ou usando Azure Policy. Um modelo de exemplo é [fornecido aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
A política de acesso de Key Vault deve permitir que o provedor de recursos de computação acesse certificados durante as implantações. Para obter etapas detalhadas, consulte [configurar Key Vault para máquinas virtuais no Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Veja a seguir um exemplo para exportar a chave pública de um certificado de autenticação, para importar para o computador.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

Uma boa referência para a criação de chaves GPG para usar com computadores Linux é fornecida por um artigo no GitHub, [gerando uma nova chave GPG](https://help.github.com/en/articles/generating-a-new-gpg-key).

Depois que o conteúdo for publicado, acrescente uma marca com o nome `GuestConfigPolicyCertificateValidation` e o valor `enabled` a todas as máquinas virtuais em que a assinatura de código deve ser necessária. Essa marca pode ser entregue em escala usando Azure Policy. Consulte a amostra [aplicar marca e seu valor padrão](../samples/apply-tag-default-value.md) . Depois que essa marca estiver em vigor, a definição de política gerada usando o cmdlet `New-GuestConfigurationPolicy` habilita o requisito por meio da extensão de configuração de convidado.

## <a name="preview-troubleshooting-guest-configuration-policy-assignments"></a>APRESENTAÇÃO Solucionando problemas de atribuições de política de configuração de convidado

Uma ferramenta está disponível na visualização para auxiliar na solução de problemas Azure Policy atribuições de configuração de convidado. A ferramenta está em versão prévia e foi publicada no Galeria do PowerShell como nome do módulo [solução de problemas de configuração de convidado](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Para obter mais informações sobre os cmdlets nesta ferramenta, use o comando Get-Help no PowerShell para mostrar as diretrizes internas. Como a ferramenta está obtendo atualizações frequentes, essa é a melhor maneira de obter as informações mais recentes.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre as VMs de auditoria com a [configuração de convidado](../concepts/guest-configuration.md).
- Entenda como [criar políticas programaticamente](programmatically-create.md).
- Saiba como [obter dados de conformidade](get-compliance-data.md).