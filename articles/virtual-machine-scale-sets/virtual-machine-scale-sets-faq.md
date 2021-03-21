---
title: FAQ dos conjuntos de dimensionamento de máquinas virtuais do Azure
description: Obtenha respostas para as perguntas mais frequentes sobre conjuntos de escala de máquina virtual em Azure.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: faq
ms.date: 06/30/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 3bc259f9ee6cb1e6fd927af82a1740403d3ae7d8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100587958"
---
# <a name="azure-virtual-machine-scale-sets-faqs"></a>FAQ dos conjuntos de dimensionamento de máquinas virtuais do Azure

Obtenha respostas para perguntas frequentes sobre conjuntos de escala de máquina virtual em Azure.

## <a name="top-frequently-asked-questions-for-scale-sets"></a>Perguntas mais frequentes para conjuntos de escala

### <a name="how-many-vms-can-i-have-in-a-scale-set"></a>Quantas VMs posso ter num conjunto de dimensionamento?

Um conjunto de escala pode ter 0 a 1.000 VMs com base em imagens da plataforma, ou 0 a 600 VMs com base em imagens personalizadas.

### <a name="are-data-disks-supported-within-scale-sets"></a>Os conjuntos de dimensionamento suportam discos de dados?

Sim. Os conjuntos de dimensionamento podem definir uma configuração de discos de dados anexados, que se aplica a todas as VMs nos conjuntos. Para obter mais informações, veja [Conjuntos de dimensionamento de VMs do Azure e discos de dados anexados](virtual-machine-scale-sets-attached-disks.md). As outras opções para armazenar dados são:

* Ficheiros do Azure (unidades SMB partilhadas)
* Unidade do sistema Operativo
* Unidade temporária (local, sem o suporte do Armazenamento do Azure)
* Serviço de dados do Azure (por exemplo, tabelas do Azure e blobs do Azure)
* Serviço de dados externo (por exemplo, base de dados remota)

### <a name="which-azure-regions-support-scale-sets"></a>Que regiões do Azure suportam os conjuntos de dimensionamento?

Todas as regiões suportam conjuntos de dimensionamento.

### <a name="how-do-i-create-a-scale-set-by-using-a-custom-image"></a>Como posso utilizar uma imagem personalizada para criar um conjunto de dimensionamento?

Crie e capture uma imagem VM e use-a como fonte para o seu conjunto de escala. Para um tutorial sobre como criar e usar uma imagem VM personalizada, você pode usar o [Azure CLI](tutorial-use-custom-image-cli.md) ou [Azure PowerShell](tutorial-use-custom-image-powershell.md)

### <a name="if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed"></a>Se reduzir a capacidade do conjunto de dimensionamento de 20 para 15, que VMs são removidas?

Por predefinição, as máquinas virtuais são removidas da escala definida uniformemente através de zonas de disponibilidade (se o conjunto de escala for implantado na configuração zonal) e domínios de avaria para maximizar a disponibilidade. São removidas primeiro as VMs com os IDs mais altos.

Pode alterar a ordem de remoção da máquina virtual especificando uma [política de escala para](virtual-machine-scale-sets-scale-in-policy.md) o conjunto de escalas.

### <a name="what-if-i-then-increase-the-capacity-from-15-to-18"></a>E se, depois, aumentar a capacidade de 15 para 18?

Se aumentar a capacidade para 18, então, são criadas três VMs novas. Sempre que isso acontecer, o ID da instância da VM é aumentado a partir do valor mais alto anterior (por exemplo, 20, 21, 22). Os VMs são equilibrados em todos os domínios de falhas.

### <a name="when-im-using-multiple-extensions-in-a-scale-set-can-i-enforce-an-execution-sequence"></a>Se utilizar várias extensões num conjunto de dimensionamento, posso forçar uma sequência de execução?

Sim, pode utilizar a sequência de [extensão](virtual-machine-scale-sets-extension-sequencing.md)definida em escala.

### <a name="do-scale-sets-work-with-azure-availability-sets"></a>Os conjuntos de dimensionamento funcionam com os conjuntos de disponibilidade do Azure?

Um conjunto de escala regional (não zonal) utiliza *grupos de colocação,* que funcionam como um conjunto de disponibilidade implícita com cinco domínios de falha e cinco domínios de atualização. Conjuntos de escala de mais de 100 VMs abrangem vários grupos de colocação. Para obter mais informações sobre os grupos de posicionamento, veja [Trabalhar com conjuntos de dimensionamento de máquinas virtuais de grande escala](virtual-machine-scale-sets-placement-groups.md). Um conjunto de disponibilidade de VMs pode existir na mesma rede virtual como um conjunto de dimensionamento de VMs. Uma configuração comum é colocar as VMs de nó de controlo (que, muitas vezes, requerem uma configuração exclusiva) num conjunto de disponibilidade e os nós de dados no conjunto de dimensionamento.

### <a name="do-scale-sets-work-with-azure-availability-zones"></a>Os conjuntos de escala funcionam com zonas de disponibilidade do Azure?

Sim! Para obter mais informações, consulte a [zona definida na escala doc](./virtual-machine-scale-sets-use-availability-zones.md).


## <a name="autoscale"></a>Dimensionamento Automático

### <a name="what-are-best-practices-for-azure-autoscale"></a>Quais são as melhores práticas para a Azure Autoscale?

Para obter as melhores práticas para a Autoscale, consulte [as melhores práticas para máquinas virtuais auto-calibradoras.](../azure-monitor/autoscale/autoscale-best-practices.md)

### <a name="where-do-i-find-metric-names-for-autoscaling-that-uses-host-based-metrics"></a>Onde encontro nomes métricos para autoscaling que usam métricas baseadas em hospedeiros?

Para obter nomes métricos para autoscaling que utilize métricas baseadas no hospedeiro, consulte [métricas suportadas com monitor Azure](../azure-monitor/essentials/metrics-supported.md).

### <a name="are-there-any-examples-of-autoscaling-based-on-an-azure-service-bus-topic-and-queue-length"></a>Existem exemplos de autoscalagem baseados num tópico de autocarro da Azure Service Bus e no comprimento da fila?

Sim. Por exemplo, a autoscalagem com base num tópico de autocarro de serviço Azure e comprimento de fila, consulte [métricas comuns de autoescalação do Azure Monitor](../azure-monitor/autoscale/autoscale-common-metrics.md).

Para uma fila de autocarros de serviço, use o seguinte JSON:

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

Para uma fila de armazenamento, utilize o seguinte JSON:

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

Substitua os valores de exemplo pelo seu recurso Uniform Resource Identifiers (URIs).


### <a name="should-i-autoscale-by-using-host-based-metrics-or-a-diagnostics-extension"></a>Devo fazer uma escala automática utilizando métricas baseadas em hospedeiros ou uma extensão de diagnóstico?

Pode criar uma definição de escala automática num VM para utilizar métricas de nível de hospedeiro ou métricas baseadas em SO.

Para obter uma lista de métricas suportadas, consulte [as métricas comuns de autoescalação do Monitor Azure](../azure-monitor/autoscale/autoscale-common-metrics.md)Monitor.

Para obter uma amostra completa para conjuntos de escala de máquina virtual, consulte [a configuração de autoescalação avançada utilizando modelos de Gestor de Recursos para conjuntos de escala de máquina virtual](../azure-monitor/autoscale/autoscale-virtual-machine-scale-sets.md).

A amostra utiliza a métrica de CPU de nível de hospedeiro e uma métrica de contagem de mensagens.



### <a name="how-do-i-set-alert-rules-on-a-virtual-machine-scale-set"></a>Como posso definir regras de alerta num conjunto de escala de máquina virtual?

Pode criar alertas em métricas para conjuntos de escala de máquinas virtuais via PowerShell ou Azure CLI. Para obter mais informações, consulte [as amostras de arranque rápido do Azure Monitor PowerShell](../azure-monitor/powershell-samples.md#create-metric-alerts) e [as amostras de arranque rápido do Azure Monitor CLI](../azure-monitor/cli-samples.md#work-with-alerts).

O TargetResourceId do conjunto de escala de máquina virtual é assim:

/subscrições/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname

Pode escolher qualquer contador de desempenho VM como métrica para definir um alerta. Para obter mais informações, consulte [as métricas de Sistema de Segurança convidado para as métricas do Windows VMs](../azure-monitor/autoscale/autoscale-common-metrics.md#guest-os-metrics-for-resource-manager-based-windows-vms) e [do Os Convidados para Os VMs Linux](../azure-monitor/autoscale/autoscale-common-metrics.md#guest-os-metrics-linux-vms) no artigo [de métricas comuns do Monitor Azure.](../azure-monitor/autoscale/autoscale-common-metrics.md)

### <a name="how-do-i-set-up-autoscale-on-a-virtual-machine-scale-set-by-using-powershell"></a>Como posso configurar uma escala de máquina virtual definida utilizando o PowerShell?

Para configurar automaticamente uma escala de máquina virtual definida utilizando o PowerShell, consulte [automaticamente um conjunto de escala de máquina virtual](tutorial-autoscale-powershell.md). Também pode configurar a autoescalação com os modelos [Azure CLI](tutorial-autoscale-cli.md) e [Azure](tutorial-autoscale-template.md)


### <a name="if-i-have-stopped-deallocated-a-vm-is-that-vm-started-as-part-of-an-autoscale-operation"></a>Se eu tiver parado (deallocated) um VM, será que a VM começou como parte de uma operação de autoescalação?

N.º Se as regras de autoescala requerem instâncias VM adicionais como parte de um conjunto de escala, é criada uma nova instância VM. As instâncias VM que são interrompidas (deallocated) não são iniciadas como parte de um evento de autoescala. No entanto, os VMs parados (deallocated) podem ser eliminados como parte de um evento de escala automática que escala no número de casos, da mesma forma que qualquer instância VM pode ser eliminada com base na ordem de identificação de instância VM.



## <a name="certificates"></a>Certificados

### <a name="how-do-i-securely-ship-a-certificate-to-the-vm"></a>Como envio um certificado para o VM?

Para enviar um certificado de forma segura para o VM, pode instalar um certificado de cliente diretamente numa loja de certificados Windows a partir do cofre-chave do cliente.

Utilize o seguinte JSON:

```json
"secrets": [
    {
        "sourceVault": {
            "id": "/subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1"
        },
        "vaultCertificates": [
            {
                "certificateUrl": "https://mykeyvault1.vault.azure.net/secrets/{secretname}/{secret-version}",
                "certificateStore": "certificateStoreName"
            }
        ]
    }
]
```

O código suporta Windows e Linux.

Para obter mais informações, consulte [Criar ou atualizar um conjunto de escala de máquina virtual](/rest/api/compute/virtualmachinescalesets/createorupdate).


### <a name="how-do-i-use-self-signed-certificates-provisioned-for-azure-service-fabric-clusters"></a>Como uso certificados auto-assinados previstos para clusters de tecido de serviço Azure?
Para o exemplo mais recente, utilize a seguinte declaração de Azure CLI dentro da concha azul, leia a documentação do módulo CLI dos Tecidos de Serviço, que será impressa para estali:

```azurecli
az sf cluster create -h
```

Os certificados auto-assinados não podem ser utilizados para a confiança distribuída fornecida por uma Autoridade de Certificados e não devem ser utilizados para qualquer Cluster de Tecidos de Serviço destinados a acolher soluções de produção empresarial; para orientação adicional de segurança do tecido de serviço, reveja [as melhores práticas de segurança do tecido](../security/fundamentals/service-fabric-best-practices.md) de serviço do serviço e os [cenários de segurança do cluster de tecido de serviço](../service-fabric/service-fabric-cluster-security.md).

### <a name="can-i-specify-an-ssh-key-pair-to-use-for-ssh-authentication-with-a-linux-virtual-machine-scale-set-from-a-resource-manager-template"></a>Posso especificar um par de chaves SSH para utilizar para a autenticação SSH com uma balança de máquina virtual Linux definida a partir de um modelo de Gestor de Recursos?

Sim. A API REST para **osProfile** é semelhante à API padrão VM REST.

Inclua **o osProfile** no seu modelo:

```json
"osProfile": {
    "computerName": "[variables('vmName')]",
    "adminUsername": "[parameters('adminUserName')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "ssh": {
            "publicKeys": [
                {
                    "path": "[variables('sshKeyPath')]",
                    "keyData": "[parameters('sshKeyData')]"
                }
            ]
        }
    }
}
```

Este bloco JSON é utilizado [neste modelo de arranque rápido Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

Para obter mais informações, consulte [Criar ou atualizar um conjunto de escala de máquina virtual](/rest/api/compute/virtualmachinescalesets/createorupdate#linuxconfiguration).

### <a name="how-do-i-remove-deprecated-certificates"></a>Como retiro certificados precotados?

Para remover certificados precotados, retire o certificado antigo da lista de certificados do cofre. Deixe todos os certificados que pretende permanecer no seu computador na lista. Isto não remove o certificado de todos os seus VMs. Também não adiciona o certificado aos novos VMs que são criados no conjunto de escala de máquina virtual.

Para remover o certificado dos VM existentes, utilize uma extensão de script personalizada para remover manualmente os certificados da sua loja de certificados.

### <a name="how-do-i-inject-an-existing-ssh-public-key-into-the-virtual-machine-scale-set-ssh-layer-during-provisioning"></a>Como posso injetar uma chave pública SSH existente na camada de SSH de escala de máquina virtual durante o provisionamento?

Se está a fornecer os VMs apenas com uma chave SSH pública, não precisa de colocar as chaves públicas no Key Vault. Chaves públicas não são segredo.

Pode fornecer chaves públicas SSH em texto simples quando criar um Linux VM:

```json
"linuxConfiguration": {
    "ssh": {
        "publicKeys": [
            {
                "path": "path",
                "keyData": "publickey"
            }
        ]
    }
}
```

nome do elemento linuxConfiguration | Necessário | Tipo | Description
--- | --- | --- | ---
ssh | No | Coleção | Especifica a configuração da chave SSH para um Sistema Operativo Linux
caminho | Sim | String | Especifica o caminho do ficheiro Linux onde as chaves ou certificado SSH devem ser localizados
keyData | Sim | String | Especifica uma chave pública SSH codificada de base64

Por exemplo, consulte [o modelo de arranque rápido gitHub de 101 vm-sshkey](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

### <a name="when-i-run-update-azvmss-after-adding-more-than-one-certificate-from-the-same-key-vault-i-see-the-following-message"></a>Quando corro `Update-AzVmss` depois de adicionar mais de um certificado do mesmo cofre, vejo a seguinte mensagem:

>Update-AzVmss: List secret contém instâncias repetidas de /subscrições/ \<my-subscription-id> /resourceGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev, que é proibido.

Isto pode acontecer se tentar voltar a adicionar o mesmo cofre em vez de usar um novo certificado de cofre para o cofre de origem existente. O `Add-AzVmssSecret` comando não funciona corretamente se estiver a adicionar segredos adicionais.

Para adicionar mais segredos a partir do mesmo cofre chave, atualize a lista de $vmss.properties.osProfile.secrets[0].vaultCertificates.

Para a estrutura de entrada esperada, consulte [Criar ou atualizar um conjunto de máquinas virtuais](/rest/api/compute/virtualmachinescalesets/createorupdate).

Encontre o segredo no objeto de escala de máquina virtual que está no cofre da chave. Em seguida, adicione a sua referência de certificado (o URL e o nome da loja secreta) à lista associada ao cofre.

> [!NOTE]
> Atualmente, não é possível remover certificados de VMs utilizando a escala de máquina virtual definida API.
>

Os novos VMs não terão o certificado antigo. No entanto, os VM que possuam o certificado e que já estão implantados terão o certificado antigo.

### <a name="can-i-push-certificates-to-the-virtual-machine-scale-set-without-providing-the-password-when-the-certificate-is-in-the-secret-store"></a>Posso empurrar certificados para a escala de máquina virtual definida sem fornecer a senha, quando o certificado está na loja secreta?

Não é necessário codificar palavras-passe nos scripts. Pode recuperar dinâmicamente palavras-passe com as permissões que utiliza para executar o script de implementação. Se tiver um script que move um certificado do cofre da loja secreta, o comando da loja secreta `get certificate` também produz a palavra-passe do ficheiro .pfx.

### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-for-a-virtual-machine-scale-set-work-why-do-i-need-the-sourcevault-value-when-i-have-to-specify-the-absolute-uri-for-a-certificate-by-using-the-certificateurl-property"></a>Como funciona a propriedade Secrets do virtualMachineProfile.osProfile para um conjunto de escala de máquina virtual? Por que preciso do valor de fonteVault quando tenho que especificar o URI absoluto para um certificado usando a propriedade certificateUrl?

Uma referência de certificado de Gestão Remota do Windows (WinRM) deve estar presente na propriedade Secrets do perfil OS.

O objetivo de indicar o cofre de origem é impor as políticas da lista de controlo de acesso (ACL) que existem no modelo Azure Cloud Service de um utilizador. Se o cofre de origem não for especificado, os utilizadores que não tenham permissões para implantar ou aceder a segredos de um cofre chave poderão fazê-lo através de um Fornecedor de Recursos Compute (CRP). Os ACLs existem mesmo para recursos que não existem.

Se fornecer um ID de cofre de origem incorreto, mas um URL de cofre de chave válido, é reportado um erro ao sondar a operação.

### <a name="if-i-add-secrets-to-an-existing-virtual-machine-scale-set-are-the-secrets-injected-into-existing-vms-or-only-into-new-ones"></a>Se eu adicionar segredos a um conjunto de escala de máquina virtual existente, os segredos são injetados em VMs existentes, ou apenas em novos?

Os certificados são adicionados a todos os seus VMs, mesmo os pré-existentes. Se a sua balança de máquina virtual definir atualizaçãoPolicy propriedade está definida como **manual,** o certificado é adicionado ao VM quando executa uma atualização manual no VM.

### <a name="where-do-i-put-certificates-for-linux-vms"></a>Onde coloco certificados para os VMs do Linux?

Para aprender a utilizar certificados para Os VMs Do Linux, consulte [os certificados de implantação em VMs a partir de um cofre-chave gerido pelo cliente.](/archive/blogs/kv/deploy-certificates-to-vms-from-customer-managed-key-vault)

### <a name="how-do-i-add-a-new-vault-certificate-to-a-new-certificate-object"></a>Como adiciono um novo certificado de cofre a um novo objeto de certificado?

Para adicionar um certificado de abóbada a um segredo existente, consulte o seguinte exemplo PowerShell. Use apenas um objeto secreto.

```powershell
$newVaultCertificate = New-AzVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809

$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)

Update-AzVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```

### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>O que acontece com os certificados se voltar a imagem de um VM?

Se voltar a imagem de um VM, os certificados são eliminados. A imagem elimina todo o disco de so.

### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>O que acontece se apagar um certificado do cofre da chave?

Se o segredo for apagado do cofre da chave, e depois `stop deallocate` correres para todos os teus VMs e depois recomeçares, encontras um falhanço. A falha ocorre porque o CRP precisa de recuperar os segredos do cofre da chave, mas não pode. Neste cenário, pode eliminar os certificados do modelo de conjunto de escala de máquina virtual.

O componente CRP não persiste nos segredos do cliente. Se correr `stop deallocate` para todos os VMs no conjunto de escala de máquina virtual, a cache é apagada. Neste cenário, os segredos são recuperados do cofre da chave.

Você não se depara com este problema ao escalonar porque há uma cópia em cache do segredo em Azure Service Fabric (no modelo de inquilino de tecido único).

### <a name="why-do-i-have-to-specify-the-certificate-version-when-i-use-key-vault"></a>Por que tenho de especificar a versão do certificado quando uso o Key Vault?

O objetivo do cofre-chave de especificar a versão do certificado é deixar claro ao utilizador qual o certificado que é implantado nos seus VMs.

Se criar um VM e atualizar o seu segredo no cofre de chaves, o novo certificado não será descarregado para os seus VMs. Mas os seus VMs parecem fazê-lo referência, e os novos VMs recebem o novo segredo. Para evitar isto, é obrigado a fazer referência a uma versão secreta.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-for-deploying-these-certificates-to-a-virtual-machine-scale-set"></a>A minha equipa trabalha com vários certificados que nos são distribuídos como .cer chaves públicas. Qual é a abordagem recomendada para a implantação destes certificados num conjunto de escala de máquina virtual?

Para implementar .cer chaves públicas num conjunto de escala de máquina virtual, pode gerar um ficheiro .pfx que contém apenas ficheiros .cer. Para isso, `X509ContentType = Pfx` use. Por exemplo, carregue o ficheiro .cer como um objeto x509Certificate2 em C# ou PowerShell e, em seguida, ligue para o método.

Para obter mais informações, consulte [X509Certificate.Export Method (X509ContentType, String)](/dotnet/api/system.security.cryptography.x509certificates.x509certificate.export?view=netcore-3.1#system_security_cryptography_x509certificates_x509certificate_export_system_security_cryptography_x509certificates_x509contenttype_system_string_).

### <a name="how-do-i-pass-in-certificates-as-base64-strings"></a>Como posso passar em certificados como cordas base64?

Para emular a passagem de um certificado como uma corda base64, pode extrair o URL mais recente num modelo de Gestor de Recursos. Inclua a seguinte propriedade JSON no seu modelo de Gestor de Recursos:

```json
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```

### <a name="do-i-have-to-wrap-certificates-in-json-objects-in-key-vaults"></a>Tenho de embrulhar certificados em objetos JSON em cofres chave?

Em conjuntos de balanças de máquinas virtuais e VMs, os certificados devem ser embrulhados em objetos JSON.

Também apoiamos a aplicação do tipo de conteúdo/x-pkcs12.

Atualmente, não suportamos ficheiros .cer. Para utilizar .cer ficheiros, exporte-os para recipientes .pfx.



## <a name="compliance-and-security"></a>Conformidade e Segurança

### <a name="are-virtual-machine-scale-sets-pci-compliant"></a>Os conjuntos de escala de máquina virtual são compatíveis com pci?

Os conjunto de dimensionamento de máquinas virtuais são uma fina camada de API sobreposta ao CRP. Os dois componentes fazem parte da plataforma de computação da árvore de serviço do Azure.

Numa perspetiva de conformidade, os conjuntos de dimensionamento de máquinas virtuais são uma parte fundamental da plataforma de computação do Azure. Partilham uma equipa, ferramentas, processos, metodologia de implementação, controlos de segurança, compilação just-in-time (JIT), monitorização, alertas e assim sucessivamente, com o próprio CRP. Os conjuntos de dimensionamento de máquinas virtuais estão conformes ao Payment Card Industry (PCI) porque o CRP faz parte do atestado da atual Norma de Segurança dos Dados (DSS) da PCI.

Para mais informações, consulte [o Microsoft Trust Center.](https://www.microsoft.com/TrustCenter/Compliance/PCI)

### <a name="does-managed-identities-for-azure-resources-work-with-virtual-machine-scale-sets"></a>As [identidades geridas para os recursos Azure](../active-directory/managed-identities-azure-resources/overview.md) funcionam com conjuntos de escala de máquinas virtuais?

Sim. Pode ver alguns modelos MSI de exemplo em modelos Azure Quickstart para [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi) e [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi).

## <a name="deleting"></a>Eliminar

### <a name="will-the-locks-i-set-in-place-on-virtual-machine-scale-set-instances-be-respected-when-deleting-instances"></a>As fechaduras que instalei em instâncias de escala de máquina virtuais serão respeitadas quando apagar casos?

No Portal Azure, tem a capacidade de eliminar uma instância individual ou apagar a granel selecionando várias instâncias. Se tentar apagar uma única instância que tenha um bloqueio no lugar, o bloqueio é respeitado e não poderá apagar o caso. No entanto, se selecionar em massa várias instâncias e qualquer uma dessas instâncias tiver um bloqueio no lugar, o(s) bloqueio(s) não será respeitado e todas as instâncias selecionadas serão eliminadas.

No Azure CLI, só tem a capacidade de apagar um caso individual. Se tentar apagar uma única instância que tenha um bloqueio no lugar, o bloqueio é respeitado e não poderá apagar esse caso.

## <a name="extensions"></a>Extensões

### <a name="how-do-i-delete-a-virtual-machine-scale-set-extension"></a>Como posso eliminar uma extensão de conjunto de escala de máquina virtual?

Para eliminar uma extensão de conjunto de escala de máquina virtual, utilize o seguinte exemplo PowerShell:

```powershell
$vmss = Get-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName"

$vmss=Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```

Pode encontrar a extensão O valor do nome em `$vmss` .

### <a name="is-there-a-virtual-machine-scale-set-template-example-that-integrates-with-azure-monitor-logs"></a>Existe um exemplo de modelo de conjunto de conjunto de máquinas virtuais que se integre com registos do Azure Monitor?

Para um exemplo de modelo de conjunto de escala de máquina virtual que se integre com registos do Monitor Azure, consulte o segundo exemplo no [Implementar um cluster de tecido de serviço Azure e permitir a monitorização utilizando registos do Monitor Azure](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric).

### <a name="how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set"></a>Como posso adicionar uma extensão a todos os VMs no meu conjunto de escala de máquina virtual?

Se a política de atualização for definida como **automática,** a recolocação do modelo com as novas propriedades de extensão atualiza todos os VMs.

Se a política de atualização estiver definida como **manual,** atualize primeiro a extensão e, em seguida, atualize manualmente todas as instâncias dos seus VM.

### <a name="if-the-extensions-associated-with-an-existing-virtual-machine-scale-set-are-updated-are-existing-vms-affected"></a>Se as extensões associadas a um conjunto de escala de máquina virtual existente forem atualizadas, os VM existentes são afetados?

Se a definição de extensão no modelo de conjunto de escala de máquina virtual for atualizada e a propriedade upgradePolicy for definida como **automática,** atualiza os VMs. Se a atualização A propriedade daPolicy estiver definida como **manual,** as extensões são sinalizadas como não correspondendo ao modelo.

### <a name="are-extensions-run-again-when-an-existing-machine-is-service-healed-or-reimaged"></a>As extensões voltam a ser executadas quando uma máquina existente é curada ou reescrurada?

Se um VM existente for curado pelo serviço, aparece como um reboot, e as extensões não são executadas novamente. Se um VM for remimagemed, o processo é semelhante substituindo a unidade de SO pela imagem de origem. Qualquer especialização do modelo mais recente, como extensões, é novamente executada.

### <a name="how-do-i-join-a-virtual-machine-scale-set-to-an-active-directory-domain"></a>Como posso juntar-me a uma escala de máquina virtual definida para um domínio ative directory?

Para juntar uma escala de máquina virtual definida para um domínio Ative Directory (AD), pode definir uma extensão.

Para definir uma extensão, utilize a propriedade JsonADDomainExtension:

```json
"extensionProfile": {
    "extensions": [
        {
            "name": "joindomain",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "JsonADDomainExtension",
                "typeHandlerVersion": "1.3",
                "settings": {
                    "Name": "[parameters('domainName')]",
                    "OUPath": "[variables('ouPath')]",
                    "User": "[variables('domainAndUsername')]",
                    "Restart": "true",
                    "Options": "[variables('domainJoinOptions')]"
                },
                "protectedsettings": {
                    "Password": "[parameters('domainJoinPassword')]"
                }
            }
        }
    ]
}
```

### <a name="my-virtual-machine-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot"></a>A minha extensão de conjunto de escala de máquina virtual está a tentar instalar algo que requer um reboot.

Se a extensão de conjunto de escala de máquina virtual estiver a tentar instalar algo que exija um reboot, pode utilizar a extensão de Configuração Estatal Desejada (DSC) da Automatização Azure Automation. Se o sistema operativo for o Windows Server 2012 R2, o Azure puxa a configuração 5.0 do Windows Management Framework (WMF) e continua com a configuração.

### <a name="how-do-i-turn-on-antimalware-in-my-virtual-machine-scale-set"></a>Como é que ligei antimalware no meu conjunto de escala de máquina virtual?

Para ligar antimalware no conjunto de escala de máquina virtual, utilize o seguinte exemplo PowerShell:

```powershell
$rgname = 'autolap'
$vmssname = 'autolapbr'
$location = 'eastus'

# Retrieve the most recent version number of the extension.
$allVersions= (Get-AzVMExtensionImage -Location $location -PublisherName "Microsoft.Azure.Security" -Type "IaaSAntimalware").Version
$versionString = $allVersions[($allVersions.count)-1].Split(".")[0] + "." + $allVersions[($allVersions.count)-1].Split(".")[1]

$VMSS = Get-AzVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname
echo $VMSS
Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "IaaSAntimalware" -Publisher "Microsoft.Azure.Security" -Type "IaaSAntimalware" -TypeHandlerVersion $versionString
Update-AzVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $VMSS
```

### <a name="how-do-i-execute-a-custom-script-thats-hosted-in-a-private-storage-account"></a>Como posso executar um script personalizado que está hospedado numa conta de armazenamento privado?

Para executar um script personalizado que está hospedado numa conta de armazenamento privado, crie definições protegidas com a chave e nome da conta de armazenamento. Para mais informações, consulte [a extensão do script personalizado.](../virtual-machines/extensions/custom-script-windows.md?toc=/azure/virtual-machines/windows/toc.json#property-managedidentity)

## <a name="passwords"></a>Palavras-passe

### <a name="how-do-i-reset-the-password-for-vms-in-my-virtual-machine-scale-set"></a>Como posso redefinir a palavra-passe para VMs no meu conjunto de escala de máquina virtual?

Existem duas formas principais de alterar a palavra-passe para VMs em conjuntos de escala.

- Mude o modelo de conjunto de escala de máquina virtual diretamente. Disponível com API 2017-12-01 e posteriormente.

    Atualize as credenciais de administração diretamente no modelo de conjunto de escala (por exemplo, utilizando o Azure Resource Explorer, PowerShell ou CLI). Uma vez atualizado o conjunto de escalas, todos os novos VMs têm as novas credenciais. Os VM existentes só têm as novas credenciais se forem reesimagem.

- Reinicie a palavra-passe utilizando as extensões de acesso VM. Certifique-se de seguir os requisitos de senha tal como descrito [aqui](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

    Utilize o exemplo do PowerShell seguinte:

    ```powershell
    $vmssName = "myvmss"
    $vmssResourceGroup = "myvmssrg"
    $publicConfig = @{"UserName" = "newuser"}
    $privateConfig = @{"Password" = "********"}

    $extName = "VMAccessAgent"
    $publisher = "Microsoft.Compute"
    $vmss = Get-AzVmss -ResourceGroupName $vmssResourceGroup -VMScaleSetName $vmssName
    $vmss = Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion "2.0" -AutoUpgradeMinorVersion $true
    Update-AzVmss -ResourceGroupName $vmssResourceGroup -Name $vmssName -VirtualMachineScaleSet $vmss
    ```

## <a name="networking"></a>Rede

### <a name="is-it-possible-to-assign-a-network-security-group-nsg-to-a-scale-set-so-that-it-applies-to-all-the-vm-nics-in-the-set"></a>É possível atribuir um Grupo de Segurança de Rede (NSG) a um conjunto de escala, de modo a que se aplique a todos os NICs VM no conjunto?

Sim. Um Grupo de Segurança de Rede pode ser aplicado diretamente a uma escala definida, referindo-a na secção de Configurações De RedeInterface do perfil de rede. Exemplo:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": "true",
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            },
                            "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

### <a name="how-do-i-do-a-vip-swap-for-virtual-machine-scale-sets-in-the-same-subscription-and-same-region"></a>Como faço uma troca VIP por conjuntos de escala de máquina virtual na mesma subscrição e na mesma região?

Se tiver dois conjuntos de escala de máquina virtual com front-ends Azure Load Balancer, e estiverem na mesma subscrição e região, pode transferir os endereços IP públicos de cada um e atribuir ao outro. Consulte [o VIP Swap: Implantação verde-azul no Azure Resource Manager,](https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/) por exemplo. Isto implica um atraso, embora os recursos sejam translocados/atribuídos ao nível da rede. Uma opção mais rápida é utilizar o Azure Application Gateway com duas piscinas de backend e uma regra de encaminhamento. Em alternativa, pode hospedar a sua aplicação com [o serviço Azure App,](https://azure.microsoft.com/services/app-service/) que fornece suporte para uma rápida troca entre slots de produção e de produção.

### <a name="how-do-i-specify-a-range-of-private-ip-addresses-to-use-for-static-private-ip-address-allocation"></a>Como especifiquei uma gama de endereços IP privados para utilizar para alocação de endereços IP privados estáticos?

Os endereços IP são selecionados a partir de uma sub-rede que especifique.

O método de atribuição de endereços IP definidos em escala de máquina virtual é sempre "dinâmico", mas isso não significa que estes endereços IP possam mudar. Neste caso, "dinâmico" significa apenas que não especifica o endereço IP num pedido PUT. Especifique o conjunto estático utilizando a sub-rede.

### <a name="how-do-i-deploy-a-virtual-machine-scale-set-to-an-existing-azure-virtual-network"></a>Como posso implantar uma escala de máquina virtual definida para uma rede virtual Azure existente?

Para implementar uma escala de máquina virtual definida para uma rede virtual Azure existente, consulte [implementar uma escala de máquina virtual definida para uma rede virtual existente.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet)

### <a name="can-i-use-scale-sets-with-accelerated-networking"></a>Posso usar conjuntos de escala com rede acelerada?

Sim. Para utilizar a rede acelerada, defina ativar a Rede de Redes a aplicar na rede de rede Do seu conjunto de escalas As definições de Configuração de Configuração do seu conjunto de escala. Por exemplo

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "niconfig1",
            "properties": {
                "primary": true,
                "enableAcceleratedNetworking" : true,
                "ipConfigurations": [
                ]
            }
        }
    ]
}
```

### <a name="how-can-i-configure-the-dns-servers-used-by-a-scale-set"></a>Como posso configurar os servidores DNS utilizados por um conjunto de escalas?

Para criar uma balança de máquina virtual definida com uma configuração de DNS personalizada, adicione um pacote JSON dnsSettings à secção de rede de conjunto de escalaInterfaceConfigurations. Exemplo:

```json
    "dnsSettings":{
        "dnsServers":["10.0.0.6", "10.0.0.5"]
    }
```

### <a name="how-can-i-configure-a-scale-set-to-assign-a-public-ip-address-to-each-vm"></a>Como posso configurar uma escala definida para atribuir um endereço IP público a cada VM?

Para criar um conjunto de escala de máquina virtual que atribua um endereço IP público a cada VM, certifique-se de que a versão API do recurso Microsoft.Compute/virtualMachineScaleSets é 2017-03-30 e adicione um pacote de _simulação de endereços publicitários_ JSON à secção ipConfigurations definida na escala. Exemplo:

```json
    "publicipaddressconfiguration": {
        "name": "pub1",
        "properties": {
        "idleTimeoutInMinutes": 15
        }
    }
```

### <a name="can-i-configure-a-scale-set-to-work-with-multiple-application-gateways"></a>Posso configurar uma escala definida para trabalhar com vários Gateways de aplicação?

Sim. Pode adicionar os IDs de recursos para vários conjuntos de endereços de backend de aplicações Gateway à lista _de aplicaçõesGatewayBackendAddressPools_ na secção _ipConfigurations_ do seu perfil de rede definido em escala.

## <a name="scale"></a>Escala

### <a name="in-what-case-would-i-create-a-virtual-machine-scale-set-with-fewer-than-two-vms"></a>Em que caso criaria uma escala de máquina virtual com menos de dois VMs?

Uma das razões para criar uma balança de máquina virtual com menos de dois VMs seria utilizar as propriedades elásticas de um conjunto de escala de máquina virtual. Por exemplo, pode implementar uma escala de máquina virtual definida com VMs zero para definir a sua infraestrutura sem pagar custos de funcionamento de VM. Em seguida, quando estiver pronto para implantar VMs, aumente a "capacidade" da escala de máquina virtual definida para a contagem de instâncias de produção.

Outra razão pela qual pode criar uma balança de máquina virtual com menos de dois VMs é se estiver menos preocupado com a disponibilidade do que em usar um conjunto de disponibilidade com VMs discretos. Os conjuntos de escala de máquina virtual dão-lhe uma forma de trabalhar com unidades de computação indiferenciadas que são fungíveis. Esta uniformidade é um diferenciador chave para conjuntos de escala de máquina virtual versus conjuntos de disponibilidade. Muitas cargas de trabalho apátridas não rastreiam unidades individuais. Se a carga de trabalho baixar, pode reduzir-se a uma unidade de computação e, em seguida, escalar até muitos quando a carga de trabalho aumenta.

### <a name="how-do-i-change-the-number-of-vms-in-a-virtual-machine-scale-set"></a>Como posso alterar o número de VMs num conjunto de escala de máquina virtual?

Para alterar o número de VMs numa balança de máquina virtual definida no portal Azure, a partir da secção de propriedades de conjunto de escala de máquina virtual, clique na lâmina "Escalar" e utilize a barra de slider.

### <a name="how-do-i-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>Como defino alertas personalizados para quando determinados limiares são atingidos?

Tem alguma flexibilidade na forma como lida com os alertas para os limiares especificados. Por exemplo, pode definir webhooks personalizados. O exemplo seguinte é de um modelo de Gestor de Recursos:

```json
{
    "type": "Microsoft.Insights/autoscaleSettings",
    "apiVersion": "[variables('insightsApi')]",
    "name": "autoscale",
    "location": "[parameters('resourceLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
    ],
    "properties": {
        "name": "autoscale",
        "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
        "enabled": true,
        "notifications": [
            {
                "operation": "Scale",
                "email": {
                    "sendToSubscriptionAdministrator": true,
                    "sendToSubscriptionCoAdministrators": true,
                    "customEmails": [
                        "youremail@address.com"
                    ]
                },
                "webhooks": [
                    {
                        "serviceUri": "<service uri>",
                        "properties": {
                            "key1": "custommetric",
                            "key2": "scalevmss"
                        }
                    }
                ]
            }
        ]
    }
}
```


## <a name="patching-and-operations"></a>Remendos e operações

### <a name="can-i-create-a-scale-set-in-an-existing-resource-group"></a>Posso criar uma escala definida num grupo de recursos existente?

Sim, pode criar uma escala definida num grupo de recursos existente.

### <a name="can-i-move-a-scale-set-to-another-resource-group"></a>Posso mover uma escala para outro grupo de recursos?

Sim, pode mover recursos definidos de escala para um novo grupo de subscrição ou recursos.

### <a name="how-to-i-update-my-virtual-machine-scale-set-to-a-new-image-how-do-i-manage-patching"></a>Como atualizar a minha escala de máquina virtual definida para uma nova imagem? Como é que consigo remendar?

Para atualizar a escala de máquina virtual definida para uma nova imagem e para gerir o patching, consulte [atualizar um conjunto de escala de máquina virtual](./virtual-machine-scale-sets-upgrade-scale-set.md).

### <a name="can-i-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-i-want-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>Posso utilizar a operação de reimagem para redefinir um VM sem alterar a imagem? (Ou seja, quero redefinir um VM nas definições de fábrica em vez de uma nova imagem.)

Sim, pode utilizar a operação de reimagem para reiniciar um VM sem alterar a imagem. No entanto, se a sua balança de máquina virtual definir referências a uma imagem de plataforma `version = latest` com, o seu VM pode atualizar para uma imagem de SO posterior quando ligar `reimage` .

### <a name="is-it-possible-to-integrate-scale-sets-with-azure-monitor-logs"></a>É possível integrar conjuntos de escala com registos do Monitor Azure?

Sim, pode instalar a extensão do Monitor Azure nos VMs definidos na escala. Aqui está um exemplo do Azure CLI:

```azurecli
az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group Team-03 --vmss-name nt01 --settings "{'workspaceId': '<your workspace ID here>'}" --protected-settings "{'workspaceKey': '<your workspace key here'}"
```

Pode encontrar o espaço de trabalho necessárioId e workspaceKey no espaço de trabalho Log Analytics do portal Azure. Na página 'Vista Geral', clique no azulejo definições. Clique no separador 'Fontes Conectadas' na parte superior.

> [!NOTE]
> Se a sua atualização de definição de _escalaPolizia_ estiver definida como Manual, tem de aplicar a extensão a todos os VMs do conjunto, chamando-lhes atualização. No CLI, esta seria _az vmss atualização-instâncias_.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="how-do-i-turn-on-boot-diagnostics"></a>Como é que faço os diagnósticos de botas?

Para ligar os diagnósticos de arranque, em primeiro lugar, crie uma conta de armazenamento. Em seguida, coloque este bloco JSON no seu conjunto virtual de escala de máquina **VirtualMachineProfile**, e atualize o conjunto de escala de máquina virtual:

```json
"diagnosticsProfile": {
    "bootDiagnostics": {
        "enabled": true,
        "storageUri": "http://yourstorageaccount.blob.core.windows.net"
    }
}
```

Quando um novo VM é criado, a propriedade InstanceView do VM mostra os detalhes para a imagem, e assim por diante. Eis um exemplo:

```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
}
```

## <a name="virtual-machine-properties"></a>Propriedades de máquinas virtuais

### <a name="how-do-i-get-property-information-for-each-vm-without-making-multiple-calls-for-example-how-would-i-get-the-fault-domain-for-each-of-the-100-vms-in-my-virtual-machine-scale-set"></a>Como consigo informações sobre propriedades para cada VM sem fazer várias chamadas? Por exemplo, como conseguiria o domínio de falhas para cada um dos 100 VMs no meu conjunto de escala de máquina virtual?

Para obter informações de propriedade para cada VM sem fazer várias chamadas, pode ligar `ListVMInstanceViews` fazendo uma API REST `GET` sobre o seguinte recurso URI:

/subscrições/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.Compute/virtualMachineScaleSets/<scaleset_name>/virtualMachines?$expand=instanceView&$select=instanceView

### <a name="can-i-pass-different-extension-arguments-to-different-vms-in-a-virtual-machine-scale-set"></a>Posso passar diferentes argumentos de extensão a diferentes VMs num conjunto de escala de máquina virtual?

Não, não é possível passar diferentes argumentos de extensão a diferentes VMs num conjunto de escala de máquina virtual. No entanto, as extensões podem agir com base nas propriedades únicas do VM em que estão a funcionar, como no nome da máquina. As extensões também podem consultar metadados de exemplo http://169.254.169.254 para obter mais informações sobre o VM.

### <a name="why-are-there-gaps-between-my-virtual-machine-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>Porque é que existem lacunas entre os nomes das máquinas VM e os IDs de VM da minha escala virtual? Por exemplo: 0, 1, 3...

Existem lacunas entre os nomes de máquinas VM e IDs de escala de máquina virtual, porque a sua propriedade **de superprovisionamento** da escala de máquina virtual está definida para o valor padrão de **verdade**. Se o excesso de provisão for definido como **verdadeiro,** são criados mais VM do que os solicitados. Os VM extra são então apagados. Neste caso, ganha uma maior fiabilidade de implantação, mas em detrimento das regras contíguas de tradução de endereços de rede contíguas (NAT).

Você pode definir esta propriedade como **falsa.** Para pequenos conjuntos de balanças de máquinas virtuais, isto não afeta significativamente a fiabilidade da implementação.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-virtual-machine-scale-set-and-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>Qual é a diferença entre eliminar um VM num conjunto de escala de máquina virtual e fazer negócios com o VM? Quando devo escolher um em vez do outro?

A principal diferença entre eliminar um VM num conjunto de escala de máquina virtual e fazer a negociação do VM é que `deallocate` não apaga os discos rígidos virtuais (VHDs). Existem custos de armazenamento associados à `stop deallocate` execução. Pode utilizar um ou outro por uma das seguintes razões:

- Queres parar de pagar os custos do cálculo, mas queres manter o estado do disco dos VMs.
- Pretende iniciar um conjunto de VMs mais rapidamente do que poderia escalar um conjunto de escala de máquina virtual.
  - Relacionado com este cenário, pode ter criado o seu próprio motor de autoescala e querer uma escala mais rápida de ponta a ponta.
- Tem um conjunto de escala de máquina virtual que é distribuído de forma desigual por domínios de falhas ou por domínios de atualização. Isto pode ser porque eliminaste seletivamente os VMs, ou porque os VM foram eliminados após uma superprovisão. Em `stop deallocate` execução seguida por `start` na balança de máquina virtual, o conjunto distribui uniformemente os VMs por domínios de avaria ou por domínios de atualização.

### <a name="how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance"></a>Como faço uma foto de uma caixa de escala de máquina virtual?
Crie uma imagem instantânea a partir de um conjunto de escala de máquina virtual.

```azurepowershell-interactive
$rgname = "myResourceGroup"
$vmssname = "myVMScaleSet"
$Id = 0
$location = "East US"

$vmss1 = Get-AzVmssVM -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $Id     
$snapshotconfig = New-AzSnapshotConfig -Location $location -AccountType Standard_LRS -OsType Windows -CreateOption Copy -SourceUri $vmss1.StorageProfile.OsDisk.ManagedDisk.id
New-AzSnapshot -ResourceGroupName $rgname -SnapshotName 'mySnapshot' -Snapshot $snapshotconfig
```

Crie um disco gerido a partir do instantâneo.

```azurepowershell-interactive
$snapshotName = "mySnapshot"
$snapshot = Get-AzSnapshot -ResourceGroupName $rgname -SnapshotName $snapshotName  
$diskConfig = New-AzDiskConfig -AccountType Premium_LRS -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
$osDisk = New-AzDisk -Disk $diskConfig -ResourceGroupName $rgname -DiskName ($snapshotName + '_Disk')
```
