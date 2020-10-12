---
title: Como criar políticas de Configuração de Convidado para o Linux
description: Saiba como criar uma política de configuração de hóspedes Azure Policy para o Linux.
ms.date: 08/17/2020
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4f49732aa2be50b0d8be6f1f3af974121dc9f363
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89076366"
---
# <a name="how-to-create-guest-configuration-policies-for-linux"></a>Como criar políticas de Configuração de Convidado para o Linux

Antes de criar políticas personalizadas, leia as informações gerais na [Configuração do Convidado Azure Policy](../concepts/guest-configuration.md).
 
Para saber sobre a criação de políticas de configuração de hóspedes para windows, consulte a página [Como criar políticas de configuração de hóspedes para windows](./guest-configuration-create.md)

Ao auditar o Linux, a Configuração de Convidado utiliza [Chef InSpec](https://www.inspec.io/). O perfil InSpec define a condição em que o computador deverá estar. Se a avaliação da configuração falhar, a auditoria do efeito **políticoIfNotExists** é ativada e a máquina é considerada **incompatível**.

[A configuração do hóspede Azure Policy](../concepts/guest-configuration.md) só pode ser usada para auditar definições dentro de máquinas. A reparação de configurações dentro das máquinas ainda não está disponível.

Utilize as seguintes ações para criar a sua própria configuração para validar o estado de uma máquina Azure ou não-Azure.

> [!IMPORTANT]
> As políticas personalizadas com a Configuração do Convidado são uma funcionalidade de pré-visualização.
>
> A extensão de Configuração de Convidado é necessária para realizar auditorias nas máquinas virtuais do Azure. Para implementar a extensão em escala em todas as máquinas Linux, atribua a seguinte definição de política: `Deploy prerequisites to enable Guest Configuration Policy on Linux VMs`

## <a name="install-the-powershell-module"></a>Instale o módulo PowerShell

O módulo de Configuração do Hóspede automatiza o processo de criação de conteúdo personalizado, incluindo:

- Criando um artefacto de conteúdo de configuração de hóspedes (.zip)
- Teste automatizado do artefacto
- Criação de uma definição de política
- Publicar a política

O módulo pode ser instalado numa máquina que executa o Windows, macOS ou Linux com o PowerShell 6.2 ou posteriormente a funcionar localmente, ou com a [Azure Cloud Shell](https://shell.azure.com), ou com a [imagem Azure PowerShell Core Docker](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> A compilação de configurações não é suportada no Linux.

### <a name="base-requirements"></a>Requisitos de base

Sistemas operativos onde o módulo pode ser instalado:

- Linux
- macOS
- Windows

> [!NOTE]
> O cmdlet 'Test-GuestConfigurationPackage' requer a versão 1.0 do OpenSSL, devido à dependência do OMI. Isto causa um erro em qualquer ambiente com OpenSSL 1.1 ou mais tarde.

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

## <a name="guest-configuration-artifacts-and-policy-for-linux"></a>Artefactos de configuração de hóspedes e política para Linux

Mesmo em ambientes Linux, a Configuração do Hóspede usa a Configuração do Estado Desejada como uma abstração linguística. A implementação baseia-se em código nativo (C++) pelo que não requer carregamento powerShell. No entanto, requer uma configuração MOF descrevendo detalhes sobre o ambiente.
A DSC está a agir como um invólucro para a InSpec uniformizar como é executada, como os parâmetros são fornecidos e como a saída é devolvida ao serviço. Pouco conhecimento da DSC é necessário ao trabalhar com conteúdo personalizado da InSpec.

#### <a name="configuration-requirements"></a>Requisitos de configuração

O nome da configuração personalizada deve ser consistente em todo o lado. O nome do ficheiro .zip para o pacote de conteúdo, o nome de configuração no ficheiro MOF e o nome de atribuição do convidado no modelo Azure Resource Manager (modelo ARM), deve ser o mesmo.

### <a name="custom-guest-configuration-configuration-on-linux"></a>Configuração de configuração personalizada do hóspede no Linux

A Configuração do Hóspede no Linux utiliza o `ChefInSpecResource` recurso para fornecer ao motor o nome do perfil [InSpec](https://www.inspec.io/docs/reference/profiles/). **O nome** é a única propriedade de recursos necessária. Crie um ficheiro YaML e um ficheiro de script Ruby, conforme descrito abaixo.

Primeiro, crie o ficheiro YaML utilizado pela InSpec. O ficheiro fornece informações básicas sobre o ambiente. Um exemplo é dado abaixo:

```YaML
name: linux-path
title: Linux path
maintainer: Test
summary: Test profile
license: MIT
version: 1.0.0
supports:
    - os-family: unix
```

Guarde este ficheiro com nome `inspec.yml` para uma pasta nomeada no seu `linux-path` diretório de projeto.

Em seguida, crie o ficheiro Ruby com a abstração linguística InSpec usada para auditar a máquina.

```Ruby
describe file('/tmp') do
    it { should exist }
end
```

Guarde este ficheiro com nome `linux-path.rb` numa nova pasta nomeada dentro do `controls` `linux-path` diretório.

Finalmente, crie uma configuração, importe o módulo de recursos **psdesiredStateConfiguration** e compile a configuração.

```powershell
# Define the configuration and import GuestConfiguration
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
        }
    }
}

# Compile the configuration to create the MOF files
import-module PSDesiredStateConfiguration
AuditFilePathExists -out ./Config
```

Guarde este ficheiro com o nome `config.ps1` na pasta do projeto. Execute-o no PowerShell executando `./config.ps1` no terminal. Será criado um novo ficheiro mof.

O `Node AuditFilePathExists` comando não é tecnicamente necessário, mas produz um ficheiro nomeado em vez do `AuditFilePathExists.mof` padrão, `localhost.mof` . Ter o nome do ficheiro .mof seguir a configuração facilita a organização de muitos ficheiros ao operar em escala.

Deve agora ter uma estrutura de projeto como abaixo:

```file
/ AuditFilePathExists
    / Config
        AuditFilePathExists.mof
    / linux-path
        linux-path.yml
        / controls
            linux-path.rb 
```

Os ficheiros de apoio devem ser embalados em conjunto. O pacote completo é utilizado pela Configuração do Hóspede para criar as definições de Política Azure.

O `New-GuestConfigurationPackage` cmdlet cria o pacote. Parâmetros do `New-GuestConfigurationPackage` cmdlet ao criar o conteúdo linux:

- **Nome**: Nome do pacote de configuração do hóspede.
- **Configuração**: Documento de configuração compilado no caminho completo.
- **Caminho**: Caminho da pasta de saída. Este parâmetro é opcional. Se não for especificado, o pacote é criado no diretório atual.
- **ChefProfilePath**: Caminho completo para o perfil InSpec. Este parâmetro só é suportado na criação de conteúdo para auditar o Linux.

Executar o seguinte comando para criar um pacote utilizando a configuração dada no passo anterior:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditFilePathExists' `
  -Configuration './Config/AuditFilePathExists.mof' `
  -ChefInSpecProfilePath './'
```

Depois de criar o pacote Configuração, mas antes de publicá-lo no Azure, pode testar a embalagem a partir da sua estação de trabalho ou ambiente CI/CD. O cmdlet GuestConfiguration `Test-GuestConfigurationPackage` inclui o mesmo agente no seu ambiente de desenvolvimento que é utilizado dentro das máquinas Azure. Utilizando esta solução, pode realizar testes de integração localmente antes de lançar para ambientes de nuvem faturada.

Uma vez que o agente está realmente a avaliar o ambiente local, na maioria dos casos você precisa executar o teste-cmdlet na mesma plataforma de SO que você planeia auditar.

Parâmetros do `Test-GuestConfigurationPackage` cmdlet:

- **Nome**: Nome da política de configuração do hóspede.
- **Parâmetro**: Parâmetros de política fornecidos em formato haxixe.
- **Caminho**: Percurso completo do pacote de Configuração de Convidados.

Executar o seguinte comando para testar o pacote criado pelo passo anterior:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditFilePathExists/AuditFilePathExists.zip
```

O cmdlet também suporta a entrada do gasoduto PowerShell. Encaneie a saída do `New-GuestConfigurationPackage` cmdlet até ao `Test-GuestConfigurationPackage` cmdlet.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditFilePathExists -Configuration ./Config/AuditFilePathExists.mof -ChefProfilePath './' | Test-GuestConfigurationPackage
```

O próximo passo é publicar o ficheiro para o armazenamento de bolhas. O script abaixo contém uma função que pode utilizar para automatizar esta tarefa. Os comandos utilizados na `publish` função requerem o `Az.Storage` módulo.

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
  -filePath ./AuditFilePathExists.zip `
  -blobName 'AuditFilePathExists'
```
Uma vez criado e carregado um pacote de política personalizada de Configuração de Convidados, crie a definição de política de Configuração de Convidados. O `New-GuestConfigurationPolicy` cmdlet pega num pacote de política personalizado e cria uma definição de política.

Parâmetros do `New-GuestConfigurationPolicy` cmdlet:

- **ContentUri**: Public http http(s) uri do pacote de conteúdo de configuração de hóspedes.
- **DisplayName**: Nome do visor da política.
- **Descrição**: Descrição da política.
- **Parâmetro**: Parâmetros de política fornecidos em formato haxixe.
- **Versão**: Versão política.
- **Caminho**: Caminho de destino onde são criadas definições políticas.
- **Plataforma**: Plataforma-alvo (Windows/Linux) para a política de configuração de hóspedes e pacote de conteúdo.
- **Tag** adiciona um ou mais filtros de etiqueta à definição de política
- **Categoria** define o campo de metadados de categoria na definição de política

O exemplo a seguir cria as definições de política num caminho especificado a partir de um pacote de política personalizado:

```azurepowershell-interactive
New-GuestConfigurationPolicy `
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Platform 'Linux' `
    -Version 1.0.0 `
    -Verbose
```

Os seguintes ficheiros são criados `New-GuestConfigurationPolicy` por:

- **auditIfNotExists.js**
- **deployIfNotExists.js**
- **Initiative.js**

A saída do cmdlet devolve um objeto que contém o nome de visualização da iniciativa e o caminho dos ficheiros de política.

Por fim, publique as definições de política utilizando o `Publish-GuestConfigurationPolicy` cmdlet. O cmdlet tem apenas o parâmetro **Path** que aponta para a localização dos ficheiros JSON criados por `New-GuestConfigurationPolicy` .

Para executar o comando Publicar, precisa de ter acesso para criar Políticas em Azure. Os requisitos específicos de autorização estão documentados na página [Azure Policy Overview.](../overview.md) O melhor papel incorporado é o **Contribuinte para a Política de Recursos.**

```azurepowershell-interactive
Publish-GuestConfigurationPolicy `
  -Path '.\policyDefinitions'
```

 O `Publish-GuestConfigurationPolicy` cmdlet aceita o caminho a partir do gasoduto PowerShell. Esta funcionalidade significa que pode criar os ficheiros de política e publicá-los num único conjunto de comandos canalizados.

 ```azurepowershell-interactive
 New-GuestConfigurationPolicy `
  -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit Linux file path.' `
  -Description 'Audit that a file path exists on a Linux machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
 ```

Com a política criada em Azure, o último passo é atribuir a iniciativa. Veja como atribuir a iniciativa ao [Portal,](../assign-policy-portal.md) [Azure CLI](../assign-policy-azurecli.md)e [Azure PowerShell.](../assign-policy-powershell.md)

> [!IMPORTANT]
> As políticas de configuração de hóspedes devem ser **sempre** atribuídas utilizando a iniciativa que combina as políticas _auditIfNotExists_ e _DeployIfNotExists._ Se apenas a política _auditIfNotExists_ for atribuída, os pré-requisitos não são implementados e a política mostra sempre que os servidores '0' estão em conformidade.

Atribuir uma definição de política com o efeito _DeployIfNotExists_ requer um nível de acesso adicional. Para conceder o menor privilégio, pode criar uma definição de papel personalizada que alarga o **Contribuinte para a Política de Recursos.** O exemplo abaixo cria uma função chamada **Resource Policy Contributor DINE** com a permissão adicional _Microsoft.Authorization/roleAssignments/write_.

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

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Usando parâmetros nas políticas personalizadas de configuração do hóspede

A Configuração do Hóspede suporta propriedades dominantes de uma Configuração no tempo de execução. Esta funcionalidade significa que os valores no ficheiro MOF na embalagem não têm de ser considerados estáticos. Os valores de sobreposição são fornecidos através da Política Azure e não têm impacto na forma como as Configurações são da autoria ou compiladas.

Com o InSpec, os parâmetros são normalmente manuseados como entrada em tempo de execução ou como código usando atributos. A Configuração do Hóspede obfunde este processo para que a entrada possa ser fornecida quando a política é atribuída. Um ficheiro de atributos é automaticamente criado dentro da máquina. Não precisa de criar e adicionar um ficheiro no seu projeto. Existem dois passos para adicionar parâmetros ao seu projeto de auditoria Linux.

Defina a entrada no ficheiro Ruby onde scripts o que auditar na máquina. Um exemplo é dado abaixo.

```Ruby
attr_path = attribute('path', description: 'The file path to validate.')

describe file(attr_path) do
    it { should exist }
end
```

Os cmdlets `New-GuestConfigurationPolicy` e `Test-GuestConfigurationPolicyPackage` incluem um parâmetro chamado **Parâmetro**. Este parâmetro requer um haxixe, incluindo todos os detalhes sobre cada parâmetro e cria automaticamente todas as secções necessárias dos ficheiros utilizados para criar cada definição de Política de Azure.

O exemplo a seguir cria uma definição de política para auditar uma trajetória de ficheiro, onde o utilizador fornece o caminho no momento da atribuição da política.

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'FilePath'                             # Policy parameter name (mandatory)
        DisplayName = 'File path.'                    # Policy parameter display name (mandatory)
        Description = "File path to be audited."      # Policy parameter description (optional)
        ResourceType = "ChefInSpecResource"           # Configuration resource type (mandatory)
        ResourceId = 'Audit Linux path exists'        # Configuration resource property name (mandatory)
        ResourcePropertyName = "AttributesYmlContent" # Configuration resource property name (mandatory)
        DefaultValue = '/tmp'                         # Policy parameter default value (optional)
    }
)

# The hashtable also supports a property named 'AllowedValues' with an array of strings to limit input to a list

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Parameter $PolicyParameterInfo `
    -Version 1.0.0
```

Para as políticas linux, inclua a propriedade **AtributoSYmlContent** na sua configuração e substitua os valores conforme necessário. O agente de Configuração de Hóspedes cria automaticamente o ficheiro YAML utilizado pela InSpec para armazenar atributos. Veja o exemplo abaixo.

```powershell
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
            AttributesYmlContent = "path: /tmp"
        }
    }
}
```

## <a name="policy-lifecycle"></a>Ciclo de vida da política

Para lançar uma atualização da definição de política, existem dois campos que requerem atenção.

- **Versão**: Quando executar o `New-GuestConfigurationPolicy` cmdlet, deve especificar um número de versão maior do que o que é publicado atualmente. A propriedade atualiza a versão da atribuição de Configuração de Hóspedes para que o agente reconheça o pacote atualizado.
- **contentHash**: Esta propriedade é atualizada automaticamente pelo `New-GuestConfigurationPolicy` cmdlet. É um valor haxixe do pacote criado `New-GuestConfigurationPackage` por. A propriedade deve estar correta para o `.zip` ficheiro que publica. Se apenas a propriedade **contentUri** for atualizada, a Extensão não aceitará o pacote de conteúdo.

A forma mais fácil de lançar um pacote atualizado é repetir o processo descrito neste artigo e fornecer um número de versão atualizado. Este processo garante que todas as propriedades foram corretamente atualizadas.


### <a name="filtering-guest-configuration-policies-using-tags"></a>Filtrar as políticas de configuração do hóspede usando Tags

As políticas criadas por cmdlets no módulo de Configuração de Convidados podem, opcionalmente, incluir um filtro para tags. O parâmetro **-Tag** suporta `New-GuestConfigurationPolicy` uma variedade de hashtables contendo conjuntos de etiquetas individuais inteiras. As etiquetas serão adicionadas à `If` secção da definição de política e não podem ser modificadas por uma atribuição de políticas.

Um exemplo de uma definição de política que irá filtrar para tags é dado abaixo.

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
      // Original Guest Configuration content will follow
    }
  ]
}
```

## <a name="optional-signing-guest-configuration-packages"></a>Opcional: Assinar pacotes de configuração de hóspedes

As políticas personalizadas de Configuração do Hóspede usam hash SHA256 para validar o pacote de política não mudou.
Opcionalmente, os clientes também podem usar um certificado para assinar pacotes e forçar a extensão de Configuração do Hóspede apenas para permitir conteúdo assinado.

Para ativar este cenário, há dois passos que precisa de completar. Executar o cmdlet para assinar o pacote de conteúdo, e anexar uma etiqueta às máquinas que devem exigir a assinatura do código.

Para utilizar a função Validação de Assinatura, execute o `Protect-GuestConfigurationPackage` cmdlet para assinar a embalagem antes de ser publicada. Este cmdlet requer um certificado de 'Assinatura de Código'.

Parâmetros do `Protect-GuestConfigurationPackage` cmdlet:

- **Caminho**: Percurso completo do pacote de Configuração de Convidados.
- **PublicGpgKeyPath**: Caminho chave GPG público. Este parâmetro só é suportado ao assinar conteúdo para Linux.

Uma boa referência para a criação de chaves GPG para usar com máquinas Linux é fornecida por um artigo sobre GitHub, [Gerando uma nova chave GPG](https://help.github.com/en/articles/generating-a-new-gpg-key).

O agente da GuestConfiguration espera que a chave pública do certificado esteja presente no caminho das `/usr/local/share/ca-certificates/extra` máquinas Linux. Para que o nó verifique o conteúdo assinado, instale a chave pública do certificado na máquina antes de aplicar a política personalizada. Este processo pode ser feito utilizando qualquer técnica dentro do VM, ou utilizando a Política Azure. Um modelo de exemplo é [fornecido aqui.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)
A política de acesso ao Cofre-Chave deve permitir ao fornecedor de recursos compute aceder a certificados durante as implementações. Para obter etapas detalhadas, consulte [Configurar o Cofre de Chaves para máquinas virtuais no Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Após a publicação do seu conteúdo, apedie uma etiqueta com nome `GuestConfigPolicyCertificateValidation` e valor a todas as `enabled` máquinas virtuais onde deve ser necessária a assinatura de código. Consulte as [amostras tags](../samples/built-in-policies.md#tags) de como as etiquetas podem ser entregues em escala usando a Política Azure. Uma vez que esta etiqueta está em vigor, a definição de política gerada através do `New-GuestConfigurationPolicy` cmdlet permite o requisito através da extensão de Configuração do Convidado.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Atribuições de política de configuração de hóspedes de resolução de problemas (visualização)

Uma ferramenta está disponível na pré-visualização para ajudar na resolução de problemas das atribuições de Configuração de Convidados Azure Policy. A ferramenta está em pré-visualização e foi publicada na PowerShell Gallery como módulo [de Resolução de Problemas de Configuração de Convidados](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Para obter mais informações sobre os cmdlets desta ferramenta, utilize o comando Get-Help em PowerShell para mostrar a orientação incorporada. Como a ferramenta está a receber atualizações frequentes, esta é a melhor maneira de obter informações mais recentes.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a auditoria de VMs com [configuração de hóspedes.](../concepts/guest-configuration.md)
- Entenda como [criar políticas programáticas.](./programmatically-create.md)
- Saiba como [obter dados de conformidade.](./get-compliance-data.md)
