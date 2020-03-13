---
title: Como criar políticas de configuração de hóspedes
description: Saiba como criar uma política azure de configuração de hóspedes para VMs Windows ou Linux com Azure PowerShell.
ms.date: 12/16/2019
ms.topic: how-to
ms.openlocfilehash: 8bd769b61ed87c9ded45ceca11586cfe105740c9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79264586"
---
# <a name="how-to-create-guest-configuration-policies"></a>Como criar políticas de configuração de hóspedes

A Configuração do Hóspede utiliza um módulo de recurso [de Configuração do Estado Desejado](/powershell/scripting/dsc/overview/overview) (DSC) para criar a configuração para auditoria das máquinas Azure. A configuração DSC define a condição em que a máquina deve estar. Se a avaliação da configuração falhar, a auditoria do efeito **policyIfNotExists** é ativada e a máquina é considerada **incompatível**.

[A configuração](../concepts/guest-configuration.md) do hóspede da Política Azure só pode ser usada para auditar definições dentro das máquinas. A reparação das definições dentro das máquinas ainda não está disponível.

Utilize as seguintes ações para criar a sua própria configuração para validar o estado de uma máquina Azure.

> [!IMPORTANT]
> As políticas personalizadas com configuração do hóspede são uma funcionalidade de pré-visualização.

## <a name="add-the-guestconfiguration-resource-module"></a>Adicione o módulo de recursos guestConfiguration

Para criar uma política de Configuração de Hóspedes, o módulo de recursos deve ser adicionado. Este módulo de recursos pode ser utilizado com powerShell instalado localmente, com [Azure Cloud Shell,](https://shell.azure.com)ou com a [imagem Azure PowerShell Core Docker](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> Enquanto o módulo **GuestConfiguration** funciona nos ambientes acima referidos, os passos para compilar uma configuração DSC devem ser concluídos no Windows PowerShell 5.1.

### <a name="base-requirements"></a>Requisitos de base

O módulo de recursos de configuração do hóspede requer o seguinte software:

- PowerShell. Se não estiver ainda instalado, siga [estas instruções](/powershell/scripting/install/installing-powershell).
- Azure PowerShell 1.5.0 ou superior. Se não estiver ainda instalado, siga [estas instruções](/powershell/azure/install-az-ps).

### <a name="install-the-module"></a>Instalar o módulo

A Configuração do Hóspede utiliza o módulo de recursos **GuestConfiguration** para criar configurações DSC e publicá-las na Política Azure:

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

## <a name="create-custom-guest-configuration-configuration-and-resources"></a>Criar configuração e recursos personalizados de configuração de hóspedes

O primeiro passo para criar uma política personalizada para a Configuração do Hóspede é criar a configuração DSC. Para uma visão geral dos conceitos dSC e terminologia, consulte [powerShell DSC visão geral](/powershell/scripting/dsc/overview/overview).

Se a sua configuração necessitar apenas de recursos que são construídos com a instalação do agente de configuração do hóspede, então só precisa de autoria de um ficheiro MOF de configuração. Se precisar de executar um script adicional, terá de ser autor de um módulo de recursos personalizados.

### <a name="requirements-for-guest-configuration-custom-resources"></a>Requisitos para recursos personalizados de configuração de hóspedes

Quando a Configuração do Hóspede audita uma máquina, executa primeiro `Test-TargetResource` para determinar se está no estado correto. O valor booleano devolvido pela função determina se o estatuto de Gestor de Recursos Azure para a Atribuição de Hóspedes deve ser conforme/não conforme. Se a booleana for `$false` para qualquer recurso na configuração, então o fornecedor executará `Get-TargetResource`. Se a booleana é `$true` então `Get-TargetResource` não é chamado.

#### <a name="configuration-requirements"></a>Requisitos de configuração

O único requisito para que a Configuração do Hóspede utilize uma configuração personalizada é que o nome da configuração seja consistente em todos os lugares onde é utilizado. Este requisito de nome inclui o nome do ficheiro .zip para o pacote de conteúdo, o nome de configuração no ficheiro MOF armazenado dentro do pacote de conteúdo, e o nome de configuração usado num modelo de Gestor de Recursos como nome de atribuição de convidados.

#### <a name="get-targetresource-requirements"></a>Requisitos de Get-TargetResource

A função `Get-TargetResource` tem requisitos especiais para configuração de hóspedes que não foram necessários para a Configuração do Estado Desejado pelo Windows.

- O hashtable que é devolvido deve incluir uma propriedade chamada **Reasons**.
- A propriedade Reasons deve ser uma matriz.
- Cada item da matriz deve ser um hashtable com teclas chamadas **Código** e **Frase**.

A propriedade Reasons é usada pelo serviço para normalizar a forma como a informação é apresentada quando uma máquina está fora de conformidade. Pode pensar em cada item em Reasons como uma "razão" para que o recurso não seja compatível. A propriedade é uma matriz porque um recurso pode estar fora de conformidade por mais de uma razão.

As propriedades **Código** e **Frase** são esperadas pelo serviço. Ao autorizar um recurso personalizado, detete o texto (tipicamente stdout) que gostaria de mostrar como a razão pela qual o recurso não é compatível como o valor para **frase**. **O Código** tem requisitos específicos de formatação, pelo que o relatório pode mostrar claramente informações sobre o recurso que foi utilizado para realizar a auditoria. Esta solução torna a Configuração do Hóspede extensível. Qualquer comando pode ser executado para auditar uma máquina desde que a saída possa ser capturada e devolvida como um valor de cadeia para a propriedade **Frase.**

- **Código** (cadeia): O nome do recurso, repetido, e depois um nome curto sem espaços como identificador pela razão. Estes três valores devem ser delimitados pelo cólon sem espaços.
  - Um exemplo seria `registry:registry:keynotpresent`
- **Frase** (corda): Texto legível pelo homem para explicar por que a definição não é compatível.
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

#### <a name="scaffolding-a-guest-configuration-project"></a>Andaimes um projeto de configuração de hóspedes

Para os desenvolvedores que gostariam de acelerar o processo de início e funcionamento a partir do código de amostra, um projeto comunitário chamado Projeto de **Configuração de Hóspedes** existe como um modelo para o módulo [Plaster](https://github.com/powershell/plaster) PowerShell. Esta ferramenta pode ser usada para andaimes um projeto, incluindo uma configuração de trabalho e recurso de amostra, e um conjunto de testes [pester](https://github.com/pester/pester) para validar o projeto. O modelo também inclui task runners para Visual Studio Code para automatizar a construção e validação do pacote de Configuração de Hóspedes. Para mais informações, consulte o projeto GitHub Projeto de [Configuração de Hóspedes](https://github.com/microsoft/guestconfigurationproject).

### <a name="custom-guest-configuration-configuration-on-linux"></a>Configuração de configuração personalizada de hóspedes em Linux

A configuração DSC para configuração de hóspedes no Linux utiliza o recurso `ChefInSpecResource` para fornecer ao motor o nome da definição [Chef InSpec.](https://www.chef.io/inspec/) **O nome** é a única propriedade de recursos necessário.

O exemplo seguinte cria uma configuração chamada **linha de base,** importa o módulo de recursos **GuestConfiguration,** e utiliza o recurso `ChefInSpecResource` definir o nome da definição InSpec para linha de base de linha de linha de **linux:**

```powershell
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

Para mais informações, consulte [Escrever, Compilar e Aplicar uma Configuração](/powershell/scripting/dsc/configurations/write-compile-apply-configuration).

### <a name="custom-guest-configuration-configuration-on-windows"></a>Configuração personalizada de configuração de hóspedes no Windows

A configuração DSC para configuração de hóspedes de política Azure é usada apenas pelo agente de configuração do hóspede, não entra em conflito com a Configuração de Estado Desejada pelo Windows PowerShell.

O exemplo seguinte cria uma configuração chamada **AuditBitLocker,** importa o módulo de recursos **GuestConfiguration,** e utiliza o recurso `Service` para auditar um serviço de execução:

```powershell
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

Para mais informações, consulte [Escrever, Compilar e Aplicar uma Configuração](/powershell/scripting/dsc/configurations/write-compile-apply-configuration).

## <a name="create-guest-configuration-custom-policy-package"></a>Criar pacote de política personalizada de configuração de hóspedes

Uma vez compilado o MOF, os ficheiros de suporte devem ser embalados em conjunto. O pacote completo é utilizado pela Configuração do Hóspede para criar as definições de Política Azure. O pacote consiste em:

- A configuração dSC compilada como um MOF
- Pasta de módulos
  - Módulo de Configuração de Hóspedes
  - Módulo DscNativeResources
  - (Linux) Uma pasta com a definição do Chef InSpec e conteúdo adicional
  - (Janelas) Módulos de recursos DSC que não são construídos em

O `New-GuestConfigurationPackage` cmdlet cria a embalagem. O seguinte formato é utilizado para criar um pacote personalizado:

```azurepowershell-interactive
New-GuestConfigurationPackage -Name '{PackageName}' -Configuration '{PathToMOF}' `
    -Path '{OutputFolder}' -Verbose
```

Parâmetros do `New-GuestConfigurationPackage` cmdlet:

- **Nome**: Nome do pacote de configuração do hóspede.
- **Configuração**: Documento de configuração DSC compilado caminho completo.
- **Caminho**: Caminho da pasta de saída. Este parâmetro é opcional. Se não especificado, o pacote é criado no diretório atual.
- **ChefProfilePath**: Caminho completo para o perfil InSpec. Este parâmetro só é suportado quando se cria conteúdo para auditar o Linux.

A embalagem completa deve ser armazenada num local acessível pelas máquinas virtuais geridas. Exemplos incluem repositórios GitHub, um Armazém Azure Repo ou Azure. Se preferir não tornar o pacote público, pode incluir um [token SAS](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md) no URL.
Também poderia implementar [o ponto final](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) de serviço para máquinas numa rede privada, embora esta configuração se aplique apenas ao acesso ao pacote e não comunicação com o serviço.

## <a name="test-a-guest-configuration-package"></a>Teste um pacote de configuração de hóspedes

Depois de criar o pacote de Configuração, mas antes de publicá-lo no Azure, pode testar a funcionalidade do pacote a partir da sua estação de trabalho ou ambiente CI/CD. O módulo GuestConfiguration inclui um cmdlet `Test-GuestConfigurationPackage` que carrega o mesmo agente no seu ambiente de desenvolvimento que é usado dentro das máquinas Azure. Utilizando esta solução, pode realizar testes de integração localmente antes de lançar para ambientes de teste/QA/produção faturados.

```azurepowershell-interactive
Test-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Verbose
```

Parâmetros do `Test-GuestConfigurationPackage` cmdlet:

- **Nome**: Nome : Nome da Política de Configuração do Hóspede.
- **Parâmetro**: Parâmetros de política fornecidos em formato hashtable.
- **Caminho**: Caminho completo do pacote de configuração de hóspedes.

O cmdlet também suporta a entrada do gasoduto PowerShell. Tubo a saída de `New-GuestConfigurationPackage` cmdlet para o `Test-GuestConfigurationPackage` cmdlet.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditWindowsService -Configuration .\DSCConfig\localhost.mof -Path .\package -Verbose | Test-GuestConfigurationPackage -Verbose
```

Para obter mais informações sobre como testar com parâmetros, consulte a secção abaixo [Usando parâmetros em políticas personalizadas de configuração de hóspedes](#using-parameters-in-custom-guest-configuration-policies).

## <a name="create-the-azure-policy-definition-and-initiative-deployment-files"></a>Criar os ficheiros de definição de política azure e implementação de iniciativas

Uma vez criado e enviado um pacote de política personalizada de configuração de hóspedes para um local acessível pelas máquinas, crie a definição de política de configuração de hóspedes para a Política Azure. O `New-GuestConfigurationPolicy` cmdlet pega num pacote de política personalizada de configuração de hóspedes acessível ao público e cria uma definição de política **auditAdaIfNotExists** e **implementaADefinição** de política IfNotExists. É também criada uma definição de iniciativa política que inclua ambas as definições políticas.

O exemplo seguinte cria as definições de política e iniciativa num caminho especificado a partir de um pacote de política personalizada de Configuração de Hóspedes para windows e fornece um nome, descrição e versão:

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

Parâmetros do `New-GuestConfigurationPolicy` cmdlet:

- **ContentUri**: Public http(s) uri do pacote de conteúdo de configuração de hóspedes.
- **Nome do ecrã**: Nome do exibição de política.
- **Descrição**: Descrição da política.
- **Parâmetro**: Parâmetros de política fornecidos em formato hashtable.
- **Versão**: Versão política.
- **Caminho**: Caminho de destino onde são criadas definições políticas.
- **Plataforma**: Plataforma alvo (Windows/Linux) para política de configuração de hóspedes e pacote de conteúdo.

Os seguintes ficheiros são criados por `New-GuestConfigurationPolicy`:

- **auditIfNotExists.json**
- **implementarIfNotExists.json**
- **Iniciativa.json**

A saída de cmdlet devolve um objeto que contém o nome e o caminho da exibição da iniciativa e do caminho dos ficheiros de política.

Se quiser usar este comando para andaimes um projeto de política personalizada, pode fazer alterações nestes ficheiros. Um exemplo seria modificar a secção "Se" para avaliar se uma etiqueta específica está presente para máquinas. Para mais detalhes sobre a criação de políticas, consulte [Programáticamente criar políticas.](./programmatically-create.md)

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Utilização de parâmetros em políticas personalizadas de configuração de hóspedes

A Configuração do Hóspede suporta propriedades dominantes de uma Configuração no tempo de execução. Esta funcionalidade significa que os valores do ficheiro MOF no pacote não têm de ser considerados estáticos. Os valores de sobreposição são fornecidos através da Política Azure e não têm impacto na forma como as Configurações são autoras ou compiladas.

Os cmdlets `New-GuestConfigurationPolicy` e `Test-GuestConfigurationPolicyPackage` incluem um parâmetro chamado **Parâmetros**. Este parâmetro tem uma definição hashtable, incluindo todos os detalhes sobre cada parâmetro e cria automaticamente todas as secções necessárias dos ficheiros utilizados para criar cada definição de Política Azure.

O exemplo seguinte criaria uma Política Azure para auditar um serviço, onde o utilizador seleciona a partir de uma lista de serviços no momento da atribuição de políticas.

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

Para as políticas do Linux, inclua a propriedade **AtributosYmlContent** na sua configuração e sobreponha os valores conforme necessário. O agente de configuração do hóspede cria automaticamente o ficheiro YAML utilizado pelo InSpec para armazenar atributos. Veja o exemplo abaixo.

```powershell
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

Para cada parâmetro adicional, adicione um hashtable à matriz. Nos ficheiros de política, verá propriedades adicionadas ao Nome de Configuração de Configuração do Hóspede que identificam o tipo de recurso, nome, propriedade e valor.

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

## <a name="publish-to-azure-policy"></a>Publicar na Política Azure

O módulo de recursos **GuestConfiguration** oferece uma forma de criar definições políticas e a definição de iniciativa em Azure com um passo através do `Publish-GuestConfigurationPolicy` cmdlet.
O cmdlet tem apenas o parâmetro **Path** que aponta para a localização dos três ficheiros JSON criados por `New-GuestConfigurationPolicy`.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions' -Verbose
```

O `Publish-GuestConfigurationPolicy` cmdlet aceita o caminho do gasoduto PowerShell. Esta funcionalidade significa que pode criar os ficheiros de política e publicá-los num único conjunto de comandos canalizados.

```azurepowershell-interactive
New-GuestConfigurationPolicy -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' -DisplayName 'Audit BitLocker service.' -Description 'Audit if the BitLocker service is not enabled on Windows machine.' -Path '.\policyDefinitions' -Platform 'Windows' -Version 1.2.3.4 -Verbose | ForEach-Object {$_.Path} | Publish-GuestConfigurationPolicy -Verbose
```

Com as definições políticas e de iniciativa criadas no Azure, o último passo é atribuir a iniciativa. Veja como atribuir a iniciativa ao [Portal,](../assign-policy-portal.md) [Azure CLI](../assign-policy-azurecli.md)e [Azure PowerShell.](../assign-policy-powershell.md)

> [!IMPORTANT]
> As políticas de Configuração de Hóspedes devem ser **sempre** atribuídas utilizando a iniciativa que combina as políticas _AuditIfNotExists_ e _DeployIfNotExists._ Se apenas for atribuída a política _AuditIfNotExists,_ os pré-requisitos não são implementados e a política mostra sempre que os servidores '0' estão em conformidade.

## <a name="policy-lifecycle"></a>Ciclo de vida político

Depois de publicar uma Política Azure personalizada usando o seu pacote de conteúdo personalizado, existem dois campos que devem ser atualizados se quiser publicar um novo lançamento.

- **Versão**: Quando executa o `New-GuestConfigurationPolicy` cmdlet, deve especificar um número de versão maior do que o atualmente publicado. A propriedade atualiza a versão da atribuição de Configuração de Hóspedes no novo ficheiro de política para que a extensão reconheça que o pacote foi atualizado.
- **conteúdoHash**: Esta propriedade é atualizada automaticamente pelo `New-GuestConfigurationPolicy` cmdlet. É um valor de hash do pacote criado por `New-GuestConfigurationPackage`. A propriedade deve estar correta para o ficheiro `.zip` que publica. Se apenas a propriedade **contentUri** for atualizada, como no caso em que alguém pode fazer uma alteração manual à definição de Política a partir do portal, a Extensão não aceitará o pacote de conteúdo.

A forma mais fácil de lançar um pacote atualizado é repetir o processo descrito neste artigo e fornecer um número de versão atualizada. Este processo garante que todas as propriedades foram corretamente atualizadas.

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>Converter conteúdo de política do Grupo Windows para configuração de hóspedes de política azure

Configuração de hóspedes, ao auditar máquinas Windows, é uma implementação da sintaxe powerShell Desired State Configuration. A comunidade DSC publicou ferramentas para converter modelos de política de grupo exportados para formato DSC. Ao utilizar esta ferramenta juntamente com os cmdlets de Configuração de Hóspedes acima descritos, pode converter conteúdo da Política do Grupo Windows e publicá-lo para a Política do Azure para auditar. Para mais detalhes sobre a utilização da ferramenta, consulte o artigo [Quickstart: Converta](/powershell/scripting/dsc/quickstarts/gpo-quickstart)a Política de Grupo em DSC .
Uma vez convertido o conteúdo, os passos acima para criar um pacote e publicá-lo como Política Azure serão os mesmos que para qualquer conteúdo DSC.

## <a name="optional-signing-guest-configuration-packages"></a>OPCIONAL: Assinar pacotes de configuração de hóspedes

As políticas personalizadas de configuração do hóspede por padrão usam haxixe SHA256 para validar o pacote de política não mudou desde a sua publicação para quando é lido pelo servidor que está a ser auditado.
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
- **PrivateGpgKeyPath**: Caminho-chave gPG privado. Este parâmetro só é suportado ao assinar conteúdo para o Linux.
- **PublicGpgKeyPath**: Caminho-chave do GPG público. Este parâmetro só é suportado ao assinar conteúdo para o Linux.

O agente GuestConfiguration espera que a chave pública do certificado esteja presente nas "Autoridades de Certificados de Raiz Fidedignas" nas máquinas Windows e no caminho `/usr/local/share/ca-certificates/extra` nas máquinas Linux. Para que o nó verifique o conteúdo assinado, instale a chave pública do certificado na máquina antes de aplicar a política personalizada. Este processo pode ser feito utilizando qualquer técnica dentro do VM, ou utilizando a Política Azure. Um modelo de exemplo é [fornecido aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
A política de acesso key vault deve permitir ao fornecedor de recursos Compute aceder a certificados durante as implementações. Para obter passos detalhados, consulte [Abóbada chave para máquinas virtuais em Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Segue-se um exemplo para exportar a chave pública de um certificado de assinatura para importar para a máquina.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

Uma boa referência para a criação de chaves GPG para usar com máquinas Linux é fornecida por um artigo sobre gitHub, [gerando uma nova chave GPG](https://help.github.com/en/articles/generating-a-new-gpg-key).

Após a publicação do seu conteúdo, anexar uma etiqueta com o nome `GuestConfigPolicyCertificateValidation` e valorizar `enabled` a todas as máquinas virtuais onde deve ser necessária a assinatura de código. Consulte as [amostras de Etiqueta](../samples/built-in-policies.md#tags) para saber como as etiquetas podem ser entregues em escala utilizando a Política Azure. Uma vez que esta etiqueta esteja em vigor, a definição de política gerada usando o `New-GuestConfigurationPolicy` cmdlet permite o requisito através da extensão de Configuração do Hóspede.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Tarefas de política de configuração de hóspedes de resolução de problemas (pré-visualização)

Uma ferramenta está disponível em pré-visualização para ajudar na resolução de tarefas de configuração de hóspedes da Política Azure. A ferramenta está em pré-visualização e foi publicada na PowerShell Gallery como nome de módulo [Guest Configuration Troubleshooter](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Para obter mais informações sobre os cmdlets nesta ferramenta, utilize o comando Get-Help na PowerShell para mostrar a orientação incorporada. Como a ferramenta está a receber atualizações frequentes, essa é a melhor maneira de obter informações mais recentes.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre auditoria de VMs com [Configuração de Hóspedes](../concepts/guest-configuration.md).
- Compreender como [criar políticas programáticas.](programmatically-create.md)
- Saiba como obter dados de [conformidade.](get-compliance-data.md)
