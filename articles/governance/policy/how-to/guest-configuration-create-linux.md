---
title: Como criar políticas de configuração de hóspedes para linux
description: Aprenda a criar uma política azure política de configuração de hóspedes para linux.
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: 65e0082f87f05104e9a57ff0342cd3d2950b63e8
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617936"
---
# <a name="how-to-create-guest-configuration-policies-for-linux"></a>Como criar políticas de configuração de hóspedes para linux

Antes de criar políticas personalizadas, leia a informação geral na Configuração de [Hóspedes de Política do Azure](../concepts/guest-configuration.md).
 
Para aprender sobre a criação de políticas de Configuração de Hóspedes para windows, consulte a página Como criar políticas de [Configuração de Hóspedes para Windows](./guest-configuration-create.md)

Ao auditar o Linux, a Configuração de Hóspedes utiliza [o Chef InSpec.](https://www.inspec.io/) O perfil InSpec define a condição em que a máquina deve estar. Se a avaliação da configuração falhar, o efeito de política **auditaIfNotExists** é desencadeado e a máquina é considerada **incompatível**.

[A configuração](../concepts/guest-configuration.md) do hóspede da Política Azure só pode ser usada para auditar definições dentro das máquinas. A reparação das definições dentro das máquinas ainda não está disponível.

Utilize as seguintes ações para criar a sua própria configuração para validar o estado de uma máquina Azure ou não Azure.

> [!IMPORTANT]
> As políticas personalizadas com configuração do hóspede são uma funcionalidade de pré-visualização.

## <a name="install-the-powershell-module"></a>Instale o módulo PowerShell

A criação de um artefacto de Configuração de Hóspedes, testes automatizados do artefacto, criação de uma definição de política e publicação da política, é inteiramente automatizado utilizando o módulo de Configuração de Hóspedes no PowerShell. O módulo pode ser instalado numa máquina que executa Windows, macOS ou Linux com PowerShell 6.2 ou mais tarde a funcionar localmente, ou com [a Azure Cloud Shell,](https://shell.azure.com)ou com a [imagem Azure PowerShell Core Docker](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> A compilação de configurações não é suportada no Linux.

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

## <a name="guest-configuration-artifacts-and-policy-for-linux"></a>Artefactos e política de configuração de hóspedes para Linux

Mesmo em ambientes Linux, a Configuração do Hóspede usa a Configuração do Estado Desejado como uma abstração de linguagem. A implementação baseia-se em código nativo (C++) por isso não necessita de carregar powerShell. No entanto, requer uma configuração MOF descrevendo detalhes sobre o ambiente. A DSC está a agir como um invólucro para o InSpec para normalizar a forma como é executado, como os parâmetros são fornecidos e como a saída é devolvida ao serviço. Pouco conhecimento do DSC é necessário quando se trabalha com conteúdo personalizado inSpec.

#### <a name="configuration-requirements"></a>Requisitos de configuração

O nome da configuração personalizada deve ser consistente em todo o lado. O nome do ficheiro .zip para o pacote de conteúdo, o nome de configuração no ficheiro MOF e o nome de atribuição de hóspedes no modelo de Gestor de Recursos, devem ser os mesmos.

### <a name="custom-guest-configuration-configuration-on-linux"></a>Configuração de configuração personalizada de hóspedes em Linux

Configuração do hóspede `ChefInSpecResource` no Linux utiliza o recurso para fornecer ao motor o nome do [perfil InSpec](https://www.inspec.io/docs/reference/profiles/). **O nome** é a única propriedade de recursos necessário. Crie um ficheiro YaML e um ficheiro de script Ruby, conforme detalhado abaixo.

Em primeiro lugar, crie o ficheiro YaML utilizado pelo InSpec. O ficheiro fornece informações básicas sobre o ambiente. Um exemplo é dado abaixo:

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

Guarde este `inspec.yml` ficheiro com `linux-path` nome para uma pasta nomeada no seu diretório de projeto.

Em seguida, crie o ficheiro Ruby com a abstração da linguagem InSpec usada para auditar a máquina.

```Ruby
describe file('/tmp') do
    it { should exist }
end
```

Guarde este `linux-path.rb` ficheiro com nome `controls` numa `linux-path` nova pasta chamada dentro do diretório.

Por fim, crie uma configuração, importe `ChefInSpecResource` o módulo de recursos **GuestConfiguration** e utilize o recurso para definir o nome do perfil InSpec.

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
AuditFilePathExists -out ./Config
```

O `Node AuditFilePathExists` comando não é tecnicamente necessário, mas `AuditFilePathExists.mof` produz um `localhost.mof`ficheiro nomeado em vez do padrão. Ter o nome de ficheiro .mof seguir a configuração torna fácil organizar muitos ficheiros ao operar em escala.

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

O `New-GuestConfigurationPackage` cmdlet cria a embalagem. Parâmetros do `New-GuestConfigurationPackage` cmdlet ao criar conteúdo Linux:

- **Nome**: Nome do pacote de configuração do hóspede.
- **Configuração**: Conjunto completo do documento de configuração compilado.
- **Caminho**: Caminho da pasta de saída. Este parâmetro é opcional. Se não especificado, o pacote é criado no diretório atual.
- **ChefProfilePath**: Caminho completo para o perfil InSpec. Este parâmetro só é suportado quando se cria conteúdo para auditar o Linux.

Executar o seguinte comando para criar uma embalagem utilizando a configuração dada no passo anterior:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditFilePathExists' `
  -Configuration './Config/AuditFilePathExists.mof'
  -ChefProfilePath './'
```

Depois de criar o pacote de configuração, mas antes de publicá-lo no Azure, pode testar o pacote a partir da sua estação de trabalho ou ambiente CI/CD. O cmdlet `Test-GuestConfigurationPackage` GuestConfiguration inclui o mesmo agente no seu ambiente de desenvolvimento que é usado dentro das máquinas Azure. Utilizando esta solução, pode realizar testes de integração localmente antes de lançar para ambientes de nuvem faturada.

Uma vez que o agente está realmente a avaliar o ambiente local, na maioria dos casos é preciso executar o Teste-cmdlet na mesma plataforma de SO que planeia auditar.

Parâmetros do `Test-GuestConfigurationPackage` cmdlet:

- **Nome**: Nome da política de configuração do hóspede.
- **Parâmetro**: Parâmetros de política fornecidos em formato hashtable.
- **Caminho**: Caminho completo do pacote de configuração de hóspedes.

Executar o seguinte comando para testar a embalagem criada pelo passo anterior:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditFilePathExists.zip
```

O cmdlet também suporta a entrada do gasoduto PowerShell. Tubo a `New-GuestConfigurationPackage` saída de cmdlet para o `Test-GuestConfigurationPackage` cmdlet.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditFilePathExists -Configuration ./Config/AuditFilePathExists.mof -ChefProfilePath './' | Test-GuestConfigurationPackage
```

O próximo passo é publicar o ficheiro no armazenamento de blob. O script abaixo contém uma função que pode utilizar para automatizar esta tarefa. Os comandos utilizados `publish` na `Az.Storage` função requerem o módulo.

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
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Platform 'Linux' `
    -Version 1.0.0 `
    -Verbose
```

Os seguintes ficheiros são criados por: `New-GuestConfigurationPolicy`

- **auditIfNotExists.json**
- **implementarIfNotExists.json**
- **Iniciativa.json**

A saída de cmdlet devolve um objeto que contém o nome e o caminho da exibição da iniciativa e do caminho dos ficheiros de política.

Por fim, publique as `Publish-GuestConfigurationPolicy` definições políticas utilizando o cmdlet.
O cmdlet tem apenas o parâmetro **Caminho** que aponta para `New-GuestConfigurationPolicy`a localização dos ficheiros JSON criados por .

Para executar o comando Publish, precisa de acesso para criar Políticas em Azure. Os requisitos específicos de autorização estão documentados na página de visão geral da [política do Azure.](../overview.md) O melhor papel incorporado é o Contribuinte da Política de **Recursos.**

```azurepowershell-interactive
Publish-GuestConfigurationPolicy `
  -Path '.\policyDefinitions'
```

 O `Publish-GuestConfigurationPolicy` cmdlet aceita o caminho do gasoduto PowerShell. Esta funcionalidade significa que pode criar os ficheiros de política e publicá-los num único conjunto de comandos canalizados.

 ```azurepowershell-interactive
 New-GuestConfigurationPolicy `
  -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit Linux file path.' `
  -Description 'Audit that a file path exists on a Linux machine.' `
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

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Utilização de parâmetros em políticas personalizadas de configuração de hóspedes

A Configuração do Hóspede suporta propriedades dominantes de uma Configuração no tempo de execução. Esta funcionalidade significa que os valores do ficheiro MOF no pacote não têm de ser considerados estáticos. Os valores de sobreposição são fornecidos através da Política Azure e não têm impacto na forma como as Configurações são autoras ou compiladas.

Com o InSpec, os parâmetros são normalmente manuseados como entrada no tempo de execução ou como código usando atributos. A Configuração do Hóspede obfusa este processo para que a entrada possa ser fornecida quando a política é atribuída. Um ficheiro de atributos é automaticamente criado dentro da máquina. Não precisa de criar e adicionar um ficheiro no seu projeto. Há dois passos para adicionar parâmetros ao seu projeto de auditoria Linux.

Defina a entrada no ficheiro Ruby onde script o que auditar na máquina. Um exemplo é dado abaixo.

```Ruby
attr_path = attribute('path', description: 'The file path to validate.')

describe file(attr_path) do
    it { should exist }
end
```

Os `New-GuestConfigurationPolicy` cmdlets `Test-GuestConfigurationPolicyPackage` e incluem um parâmetro chamado **Parâmetros**. Este parâmetro toma um hashtable, incluindo todos os detalhes sobre cada parâmetro e cria automaticamente todas as secções necessárias dos ficheiros utilizados para criar cada definição de Política Azure.

O exemplo seguinte cria uma definição de política para auditar uma trajetória de ficheiro, onde o utilizador fornece o caminho no momento da atribuição de políticas.

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
    -Parameters $PolicyParameterInfo `
    -Version 1.0.0
```

Para as políticas do Linux, inclua a propriedade **AtributosYmlContent** na sua configuração e sobreponha os valores conforme necessário. O agente de configuração do hóspede cria automaticamente o ficheiro YAML utilizado pelo InSpec para armazenar atributos. Veja o exemplo abaixo.

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

## <a name="policy-lifecycle"></a>Ciclo de vida político

Para lançar uma atualização da definição de política, há dois campos que requerem atenção.

- **Versão**: Quando `New-GuestConfigurationPolicy` executa o cmdlet, deve especificar um número de versão maior do que o atualmente publicado. A propriedade atualiza a versão da atribuição de Configuração de Hóspedes para que o agente reconheça o pacote atualizado.
- **conteúdoHash**: Esta propriedade é `New-GuestConfigurationPolicy` atualizada automaticamente pelo cmdlet. É um valor hash do pacote `New-GuestConfigurationPackage`criado por . A propriedade deve estar `.zip` correta para o ficheiro que publica. Se apropriedade **de conteúdoUri** for atualizada, a Extensão não aceitará o pacote de conteúdo.

A forma mais fácil de lançar um pacote atualizado é repetir o processo descrito neste artigo e fornecer um número de versão atualizada. Este processo garante que todas as propriedades foram corretamente atualizadas.

## <a name="optional-signing-guest-configuration-packages"></a>Opcional: Assinar pacotes de configuração de hóspedes

As políticas personalizadas de configuração do hóspede usam haxixe SHA256 para validar o pacote de política não mudou.
Opcionalmente, os clientes também podem usar um certificado para assinar pacotes e forçar a extensão de Configuração de Hóspedes apenas permitir conteúdo assinado.

Para permitir este cenário, há dois passos que precisa de completar. Executar o cmdlet para assinar o pacote de conteúdo e anexar uma etiqueta às máquinas que devem exigir a assinatura do código.

Para utilizar a função `Protect-GuestConfigurationPackage` Signature Validação, execute o cmdlet para assinar o pacote antes de ser publicado. Este cmdlet requer um certificado de "Assinatura de Código".

Parâmetros do `Protect-GuestConfigurationPackage` cmdlet:

- **Caminho**: Caminho completo do pacote de configuração de hóspedes.
- **PublicGpgKeyPath**: Caminho-chave do GPG público. Este parâmetro só é suportado ao assinar conteúdo para o Linux.

Uma boa referência para a criação de chaves GPG para usar com máquinas Linux é fornecida por um artigo sobre gitHub, [gerando uma nova chave GPG](https://help.github.com/en/articles/generating-a-new-gpg-key).

O agente guestConfiguration espera que a chave `/usr/local/share/ca-certificates/extra` pública do certificado esteja presente no caminho das máquinas Linux. Para que o nó verifique o conteúdo assinado, instale a chave pública do certificado na máquina antes de aplicar a política personalizada. Este processo pode ser feito utilizando qualquer técnica dentro do VM, ou utilizando a Política Azure. Um modelo de exemplo é [fornecido aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
A política de acesso key vault deve permitir ao fornecedor de recursos Compute aceder a certificados durante as implementações. Para obter passos detalhados, consulte [Abóbada chave para máquinas virtuais em Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Após a publicação do seu conteúdo, `GuestConfigPolicyCertificateValidation` acomode uma etiqueta com nome e valor `enabled` a todas as máquinas virtuais onde deve ser necessária a assinatura de código. Consulte as [amostras de Etiqueta](../samples/built-in-policies.md#tags) para saber como as etiquetas podem ser entregues em escala utilizando a Política Azure. Uma vez que esta etiqueta esteja em `New-GuestConfigurationPolicy` vigor, a definição de política gerada usando o cmdlet permite o requisito através da extensão de Configuração do Hóspede.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Tarefas de política de configuração de hóspedes de resolução de problemas (pré-visualização)

Uma ferramenta está disponível em pré-visualização para ajudar na resolução de tarefas de configuração de hóspedes da Política Azure. A ferramenta está em pré-visualização e foi publicada na PowerShell Gallery como nome de módulo [Guest Configuration Troubleshooter](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Para obter mais informações sobre os cmdlets nesta ferramenta, utilize o comando Get-Help na PowerShell para mostrar a orientação incorporada. Como a ferramenta está a receber atualizações frequentes, essa é a melhor maneira de obter informações mais recentes.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre auditoria de VMs com [Configuração de Hóspedes](../concepts/guest-configuration.md).
- Compreender como [criar políticas programáticas.](programmatically-create.md)
- Saiba como obter dados de [conformidade.](get-compliance-data.md)
