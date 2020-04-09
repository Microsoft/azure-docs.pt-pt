---
title: FAQ dos conjuntos de dimensionamento de máquinas virtuais do Azure
description: Obtenha respostas às perguntas mais frequentes sobre conjuntos de escala de máquinas virtuais em Azure.
author: mayanknayar
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: manayar
ms.openlocfilehash: 3b34708d6e91da627ff44018778318337ddb6b06
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879501"
---
# <a name="azure-virtual-machine-scale-sets-faqs"></a>FAQ dos conjuntos de dimensionamento de máquinas virtuais do Azure

Obtenha respostas a perguntas frequentes sobre conjuntos de escala de máquinas virtuais em Azure.

## <a name="top-frequently-asked-questions-for-scale-sets"></a>Top perguntas frequentemente feitas para conjuntos de escala

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

Crie e capture uma imagem VM, em seguida, use-a como fonte para o seu conjunto de escala. Para um tutorial sobre como criar e usar uma imagem VM personalizada, pode utilizar o [Azure CLI](tutorial-use-custom-image-cli.md) ou [Azure PowerShell](tutorial-use-custom-image-powershell.md)

### <a name="if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed"></a>Se reduzir a capacidade do conjunto de dimensionamento de 20 para 15, que VMs são removidas?

As máquinas virtuais são removidas do conjunto de dimensionamento de forma uniforme entre domínios de atualização e domínios de falha, para maximizar a disponibilidade. São removidas primeiro as VMs com os IDs mais altos.

### <a name="what-if-i-then-increase-the-capacity-from-15-to-18"></a>E se, depois, aumentar a capacidade de 15 para 18?

Se aumentar a capacidade para 18, então, são criadas três VMs novas. Sempre que isso acontecer, o ID da instância da VM é aumentado a partir do valor mais alto anterior (por exemplo, 20, 21, 22). As VMs são balanceadas entre os domínios de falhas e os domínios de atualização.

### <a name="when-im-using-multiple-extensions-in-a-scale-set-can-i-enforce-an-execution-sequence"></a>Se utilizar várias extensões num conjunto de dimensionamento, posso forçar uma sequência de execução?

Sim, pode utilizar [a seqüência](virtual-machine-scale-sets-extension-sequencing.md)de extensão definida pela balança.

### <a name="do-scale-sets-work-with-azure-availability-sets"></a>Os conjuntos de dimensionamento funcionam com os conjuntos de disponibilidade do Azure?

Um conjunto de escala regional (não zonal) utiliza grupos de *colocação,* que funcionam como um conjunto de disponibilidade implícita com cinco domínios de falha e cinco domínios de atualização. Conjuntos de escala de mais de 100 VMs abrangem vários grupos de colocação. Para obter mais informações sobre os grupos de posicionamento, veja [Trabalhar com conjuntos de dimensionamento de máquinas virtuais de grande escala](virtual-machine-scale-sets-placement-groups.md). Um conjunto de disponibilidade de VMs pode existir na mesma rede virtual como um conjunto de dimensionamento de VMs. Uma configuração comum é colocar as VMs de nó de controlo (que, muitas vezes, requerem uma configuração exclusiva) num conjunto de disponibilidade e os nós de dados no conjunto de dimensionamento.

### <a name="do-scale-sets-work-with-azure-availability-zones"></a>Os conjuntos de escala funcionam com zonas de disponibilidade do Azure?

Sim! Para mais informações, consulte a zona definida em [escala doc](./virtual-machine-scale-sets-use-availability-zones.md).


## <a name="autoscale"></a>Dimensionamento Automático

### <a name="what-are-best-practices-for-azure-autoscale"></a>Quais são as melhores práticas para a Azure Autoscale?

Para obter as melhores práticas para autoescala, consulte [as melhores práticas para autoscalcificar máquinas virtuais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-best-practices).

### <a name="where-do-i-find-metric-names-for-autoscaling-that-uses-host-based-metrics"></a>Onde encontro nomes métricos para autoscalcificação que usam métricas baseadas em hospedeiros?

Para nomes métricos para autoscalcificação que utilizam métricas baseadas em hospedeiros, consulte [métricas suportadas com o Monitor Azure](https://azure.microsoft.com/documentation/articles/monitoring-supported-metrics/).

### <a name="are-there-any-examples-of-autoscaling-based-on-an-azure-service-bus-topic-and-queue-length"></a>Existem exemplos de autoscalcificação baseado supor um tópico de ônibus de serviço Azure e comprimento de fila?

Sim. Por exemplo, de autoscalcificação com base num tópico de ônibus de serviço Azure e comprimento de fila, consulte [as métricas comuns do Azure Monitor.](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/)

Para uma fila de ônibus de serviço, utilize o seguinte JSON:

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

Substitua os valores de exemplo por os seus identificadores uniformes de recursos (URIs).


### <a name="should-i-autoscale-by-using-host-based-metrics-or-a-diagnostics-extension"></a>Devo escalar automaticamente usando métricas baseadas em hospedeiro ou uma extensão de diagnóstico?

Você pode criar uma definição de escala automática em um VM para usar métricas de nível de anfitrião ou métricas baseadas em OS convidados.

Para obter uma lista de métricas suportadas, consulte o [Azure Monitor a automatizar métricas comuns](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-common-metrics).

Para obter uma amostra completa para conjuntos de escala de máquina virtual, consulte a configuração avançada de escala automática utilizando modelos de Gestor de [Recursos para conjuntos de escala](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets)de máquina virtual .

A amostra utiliza a métrica cpu de nível hospedeiro e uma métrica de contagem de mensagens.



### <a name="how-do-i-set-alert-rules-on-a-virtual-machine-scale-set"></a>Como posso definir regras de alerta num conjunto de escala de máquina virtual?

Pode criar alertas sobre métricas para conjuntos de escala de máquinas virtuais através do PowerShell ou do Azure CLI. Para mais informações, consulte [as amostras de arranque rápido do Azure Monitor PowerShell](https://azure.microsoft.com/documentation/articles/insights-powershell-samples/#create-alert-rules) e as [amostras de arranque rápido CLI](https://azure.microsoft.com/documentation/articles/insights-cli-samples/#work-with-alerts)da plataforma transversal Azure Monitor .

O TargetResourceId do conjunto de escala de máquina virtual é assim:

/subscrições/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname

Pode escolher qualquer contador de desempenho VM como métrica para definir um alerta. Para obter mais informações, consulte as métricas do Os do Guest PARA Os de [Recursos Baseados em Windows VMs](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-resource-manager-based-windows-vms) e [métricas de Os convidados para VMs Linux](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-linux-vms) no artigo de [métricas comuns do Monitor Descalcificação Azure Monitor.](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/)

### <a name="how-do-i-set-up-autoscale-on-a-virtual-machine-scale-set-by-using-powershell"></a>Como configurar a escala automática numa escala de máquina virtual definida utilizando o PowerShell?

Para configurar a escala automática numa escala de máquina virtual definida utilizando o PowerShell, consulte [automaticamente uma balança](tutorial-autoscale-powershell.md)de escala virtual . Também pode configurar a escala automática com os modelos [Azure CLI](tutorial-autoscale-cli.md) e [Azure](tutorial-autoscale-template.md)


### <a name="if-i-have-stopped-deallocated-a-vm-is-that-vm-started-as-part-of-an-autoscale-operation"></a>Se parei (locom) um VM, a VM começou como parte de uma operação à escala automática?

Não. Se as regras de escala automática exigirem instâncias VM adicionais como parte de um conjunto de escala, é criada uma nova instância vM. Os casos vm que são interrompidos (deallocated) não são iniciados como parte de um evento de escala automática. No entanto, os VMs parados (deallocated) podem ser eliminados como parte de um evento de escala automática que escala no número de instâncias, da mesma forma que qualquer instância vm pode ser eliminada com base na ordem de ID da instância VM.



## <a name="certificates"></a>Certificados

### <a name="how-do-i-securely-ship-a-certificate-to-the-vm"></a>Como posso enviar um certificado para o VM?

Para enviar um certificado de forma segura para o VM, pode instalar um certificado de cliente diretamente numa loja de certificados Windows a partir do cofre chave do cliente.

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

Para mais informações, consulte [Criar ou atualizar um conjunto](https://msdn.microsoft.com/library/mt589035.aspx)de escala de máquina virtual .


### <a name="how-do-i-use-self-signed-certificates-provisioned-for-azure-service-fabric-clusters"></a>Como uso certificados auto-assinados previstos para clusters de tecidos de serviço Azure?
Para o mais recente exemplo, utilize a seguinte declaração cli azul dentro da concha azul, leia o módulo CLI de tecidos de serviço Exemplo documentação, que será impressa para stdout:

```azurecli
az sf cluster create -h
```

Os certificados auto-assinados não podem ser utilizados para a confiança distribuída fornecida por uma Autoridade de Certificados, não devendo ser utilizados para qualquer Cluster de Tecidos de Serviço destinado a acolher soluções de produção empresarial; para orientação adicional de segurança de tecido de serviço, reveja as [melhores práticas](https://docs.microsoft.com/azure/security/fundamentals/service-fabric-best-practices) de segurança do tecido do serviço Azure e os cenários de segurança do cluster de tecido de [serviço.](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/)

### <a name="can-i-specify-an-ssh-key-pair-to-use-for-ssh-authentication-with-a-linux-virtual-machine-scale-set-from-a-resource-manager-template"></a>Posso especificar um par de chaves SSH para usar para autenticação SSH com uma escala de máquina virtual Linux definida a partir de um modelo de Gestor de Recursos?

Sim. A API REST para **osProfile** é semelhante à API vm REST padrão.

Incluir **osPerfil** no seu modelo:

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

Este bloco JSON é utilizado neste modelo de [arranque rápido Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

Para mais informações, consulte [Criar ou atualizar um conjunto](https://msdn.microsoft.com/library/azure/mt589035.aspx#linuxconfiguration)de escala de máquina virtual .

### <a name="how-do-i-remove-deprecated-certificates"></a>Como retiro certificados precados?

Para remover certificados premeditados, retire o certificado antigo da lista de certificados do cofre. Deixe todos os certificados que pretende permanecer no seu computador na lista. Isto não remove o certificado de todos os seus VMs. Também não adiciona o certificado aos novos VMs que são criados no conjunto de escala de máquina virtual.

Para remover o certificado dos VMs existentes, utilize uma extensão de script personalizada para remover manualmente os certificados da sua loja de certificados.

### <a name="how-do-i-inject-an-existing-ssh-public-key-into-the-virtual-machine-scale-set-ssh-layer-during-provisioning"></a>Como injeto uma chave pública SSH existente na camada ssh de escala virtual durante o fornecimento?

Se está a fornecer aos VMs apenas uma chave SSH pública, não precisa de colocar as chaves públicas no Cofre chave. As chaves públicas não são secretas.

Você pode fornecer chaves públicas SSH em texto simples quando criar um VM Linux:

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

nome do elemento linuxConfiguration | Necessário | Tipo | Descrição
--- | --- | --- | ---
ssh | Não | Coleção | Especifica a configuração da chave SSH para um Sistema Operativo Linux
path | Sim | String | Especifica o caminho do ficheiro Linux onde devem ser localizadas as chaves ou certificadoS SSH
keyData | Sim | String | Especifica uma chave pública SSH codificada com base64

Por exemplo, consulte o modelo de [arranque rápido GitHub de 101-vm-sshkey](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

### <a name="when-i-run-update-azvmss-after-adding-more-than-one-certificate-from-the-same-key-vault-i-see-the-following-message"></a>Quando corro `Update-AzVmss` depois de adicionar mais de um certificado do mesmo cofre, vejo a seguinte mensagem:

>Update-AzVmss: List secret contém instâncias repetidas de /subscrições/\<my-subscrição-id>/recursosGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev, o que é proibido.

Isto pode acontecer se tentar readicionar o mesmo cofre em vez de usar um novo certificado de cofre para o cofre de origem existente. O `Add-AzVmssSecret` comando não funciona corretamente se estiver a adicionar segredos adicionais.

Para adicionar mais segredos do mesmo cofre de chaves, atualize a lista de certificados $vmss.properties.osProfile.secrets[0].vaultCertificates.

Para a estrutura de entrada esperada, consulte Criar ou atualizar um conjunto de [máquinas virtuais](https://msdn.microsoft.com/library/azure/mt589035.aspx).

Encontre o segredo no objeto de escala de máquina virtual que está no cofre da chave. Em seguida, adicione a referência do certificado (o URL e o nome da loja secreta) à lista associada ao cofre.

> [!NOTE]
> Atualmente, não é possível remover certificados de VMs utilizando a escala virtual de escala de máquina API.
>

Os novos VMs não terão o certificado antigo. No entanto, os VMs que possuam o certificado e que já estão implantados terão o certificado antigo.

### <a name="can-i-push-certificates-to-the-virtual-machine-scale-set-without-providing-the-password-when-the-certificate-is-in-the-secret-store"></a>Posso colocar certificados na escala virtual da máquina definida sem fornecer a senha, quando o certificado está na loja secreta?

Não é preciso codificar palavras-passe em scripts. Pode recuperar dinamicamente palavras-passe com as permissões que utiliza para executar o script de implementação. Se tiver um script que mova um certificado do cofre `get certificate` da loja secreta, o comando da loja secreta também produz a palavra-passe do ficheiro .pfx.

### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-for-a-virtual-machine-scale-set-work-why-do-i-need-the-sourcevault-value-when-i-have-to-specify-the-absolute-uri-for-a-certificate-by-using-the-certificateurl-property"></a>Como funciona a propriedade Secrets de virtualMachineProfile.osProfile para um conjunto de escala de máquina virtual? Por que preciso do valor sourceVault quando tenho que especificar o URI absoluto para um certificado usando a propriedade certificateUrl?

Uma referência de certificado de Gestão Remota do Windows (WinRM) deve estar presente na propriedade Secrets do perfil OS.

O objetivo de indicar o cofre de origem é impor as políticas da lista de controlo de acesso (ACL) que existem no modelo de Serviço de Nuvem Azure de um utilizador. Se o cofre de origem não for especificado, os utilizadores que não tenham permissão para implementar ou aceder a segredos para um cofre chave seriam capazes de o fazer através de um Fornecedor de Recursos Compute (CRP). Os ACLs existem mesmo para recursos que não existem.

Se fornecer um ID de cofre de origem incorreto, mas um URL de cofre de chave válido, um erro é relatado quando faz uma sondagem.

### <a name="if-i-add-secrets-to-an-existing-virtual-machine-scale-set-are-the-secrets-injected-into-existing-vms-or-only-into-new-ones"></a>Se eu adicionar segredos a um conjunto de máquinas virtuais existente, os segredos são injetados em VMs existentes, ou apenas em novos?

Os certificados são adicionados a todos os seus VMs, mesmo os pré-existentes. Se a sua atualização de escala **manual**de máquina virtual configurar a propriedade Está definida manualmente, o certificado é adicionado ao VM quando executa uma atualização manual no VM.

### <a name="where-do-i-put-certificates-for-linux-vms"></a>Onde coloco certificados para Os VMs linux?

Para aprender a implementar certificados para VMs Linux, consulte [A utilização de certificados para VMs a partir de um cofre de chaves gerido pelo cliente](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/).

### <a name="how-do-i-add-a-new-vault-certificate-to-a-new-certificate-object"></a>Como posso adicionar um novo certificado de cofre a um novo objeto de certificado?

Para adicionar um certificado de cofre a um segredo existente, consulte o seguinte exemplo powerShell. Use apenas um objeto secreto.

```powershell
$newVaultCertificate = New-AzVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809

$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)

Update-AzVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```

### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>O que acontece aos certificados se reimagem de um VM?

Se reimagem de um VM, os certificados são eliminados. A reimagem elimina todo o disco OS.

### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>O que acontece se apagar um certificado do cofre?

Se o segredo for apagado do cofre `stop deallocate` das chaves, e correres para todos os teus VMs e depois recomeças, encontras um fracasso. A falha ocorre porque o CRP precisa de recuperar os segredos do cofre, mas não pode. Neste cenário, pode eliminar os certificados do modelo de conjunto de máquinas virtuais.

A componente CRP não persiste nos segredos dos clientes. Se correr `stop deallocate` para todos os VMs no conjunto de escala de máquina virtual, a cache é eliminada. Neste cenário, segredos são recuperados do cofre chave.

Não se encontra este problema ao escalonar porque há uma cópia em cache do segredo em Azure Service Fabric (no modelo de inquilino de tecido único).

### <a name="why-do-i-have-to-specify-the-certificate-version-when-i-use-key-vault"></a>Por que tenho que especificar a versão do certificado quando uso o Cofre chave?

O objetivo do cofre chave para especificar a versão do certificado é deixar claro ao utilizador qual o certificado que é implantado nos seus VMs.

Se criar um VM e, em seguida, atualizar o seu segredo no cofre da chave, o novo certificado não é descarregado para os seus VMs. Mas os seus VMs parecem referenciar, e os novos VMs recebem o novo segredo. Para evitar isto, é-lhe exigido que refira uma versão secreta.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-for-deploying-these-certificates-to-a-virtual-machine-scale-set"></a>A minha equipa trabalha com vários certificados que nos são distribuídos como chaves públicas.cer. Qual é a abordagem recomendada para a implementação destes certificados num conjunto de escala de máquina virtual?

Para implementar as teclas públicas .cer para um conjunto de escala de máquina virtual, pode gerar um ficheiro .pfx que contém apenas ficheiros .cer. Para isso, `X509ContentType = Pfx`use. Por exemplo, carregue o ficheiro .cer como um objeto x509Certificate2 em C# ou PowerShell e, em seguida, ligue para o método.

Para mais informações, consulte [X509Certificate.Export Method (X509ContentType, String)](https://msdn.microsoft.com/library/24ww6yzk(v=vs.110.aspx)).

### <a name="how-do-i-pass-in-certificates-as-base64-strings"></a>Como passo em certificados como cordas base64?

Para emular um certificado como uma cadeia base64, pode extrair o URL mais recente versão do modelo de Gestor de Recursos. Inclua a seguinte propriedade JSON no seu modelo de Gestor de Recursos:

```json
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```

### <a name="do-i-have-to-wrap-certificates-in-json-objects-in-key-vaults"></a>Tenho de embrulhar certificados em objetos JSON em cofres chave?

Em conjuntos de escala de máquinas virtuais e VMs, os certificados devem ser embrulhados em objetos JSON.

Também apoiamos a aplicação do tipo de conteúdo/x-pkcs12.

Atualmente não suportamos ficheiros .cer. Para utilizar ficheiros .cer, exporte-os para recipientes .pfx.



## <a name="compliance-and-security"></a>Conformidade e Segurança

### <a name="are-virtual-machine-scale-sets-pci-compliant"></a>A escala virtual de máquinas é compatível com PCI?

Os conjunto de dimensionamento de máquinas virtuais são uma fina camada de API sobreposta ao CRP. Os dois componentes fazem parte da plataforma de computação da árvore de serviço do Azure.

Numa perspetiva de conformidade, os conjuntos de dimensionamento de máquinas virtuais são uma parte fundamental da plataforma de computação do Azure. Partilham uma equipa, ferramentas, processos, metodologia de implementação, controlos de segurança, compilação just-in-time (JIT), monitorização, alertas e assim sucessivamente, com o próprio CRP. Os conjuntos de dimensionamento de máquinas virtuais estão conformes ao Payment Card Industry (PCI) porque o CRP faz parte do atestado da atual Norma de Segurança dos Dados (DSS) da PCI.

Para mais informações, consulte [o Microsoft Trust Center](https://www.microsoft.com/TrustCenter/Compliance/PCI).

### <a name="does-managed-identities-for-azure-resources-work-with-virtual-machine-scale-sets"></a>As [identidades geridas para os recursos do Azure](https://docs.microsoft.com/azure/active-directory/msi-overview) funcionam com conjuntos de escala de máquinas virtuais?

Sim. Pode ver alguns modelos de MSI exemplo em modelos Azure Quickstart para [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi) e [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi).

## <a name="deleting"></a>Apagando 

### <a name="will-the-locks-i-set-in-place-on-virtual-machine-scale-set-instances-be-respected-when-deleting-instances"></a>As fechaduras que coloquei em instâncias de conjunto de máquinas virtuais serão respeitadas ao apagar casos?

No Portal Azure, tem a capacidade de eliminar uma instância individual ou apagar a granel selecionando várias instâncias. Se tentar eliminar uma única instância que tenha um cadeado no lugar, o bloqueio é respeitado e não poderá apagar a instância. No entanto, se selecionar várias instâncias em massa e qualquer uma dessas instâncias tiver um bloqueio no lugar, o bloqueio(s) não será respeitado e todas as instâncias selecionadas serão eliminadas. 
 
No Azure CLI, só tem a capacidade de eliminar uma instância individual. Se tentar eliminar uma única instância que tenha um cadeado no lugar, o bloqueio é respeitado e não poderá eliminar essa instância. 

## <a name="extensions"></a>Extensões

### <a name="how-do-i-delete-a-virtual-machine-scale-set-extension"></a>Como posso apagar uma extensão de conjunto de máquinavirtual?

Para eliminar uma extensão de conjunto de escala de máquina virtual, utilize o seguinte exemplo PowerShell:

```powershell
$vmss = Get-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName"

$vmss=Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```

Pode encontrar o valor `$vmss`do nome de extensão em .

### <a name="is-there-a-virtual-machine-scale-set-template-example-that-integrates-with-azure-monitor-logs"></a>Existe um exemplo de modelo de modelo de escala de máquina virtual que se integra com registos do Monitor Azure?

Para um exemplo de modelo de modelo de conjunto de escala de máquina virtual que se integra com registos do Monitor Azure, consulte o segundo exemplo em implementar um cluster de tecido de [serviço Azure e permitir a monitorização utilizando registos do Monitor Azure](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric).

### <a name="how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set"></a>Como adiciono uma extensão a todos os VMs no meu conjunto de máquinas virtuais?

Se a política de atualização estiver definida para **automática,** reimplantando o modelo com as novas propriedades de extensão atualiza todos os VMs.

Se a política de atualização estiver definida para **manual,** atualize primeiro a extensão e, em seguida, atualize manualmente todas as instâncias dos seus VMs.

### <a name="if-the-extensions-associated-with-an-existing-virtual-machine-scale-set-are-updated-are-existing-vms-affected"></a>Se as extensões associadas a um conjunto de escala de máquina virtual existente forem atualizadas, os VMexistentes são afetados?

Se a definição de extensão no modelo de conjunto de escala de máquina virtual for atualizada e a propriedade de actualizaçãoPolítica for definida como **automática,** atualiza os VMs. Se a propriedade upgradePolítica for definida para **manual**, as extensões são sinalizadas como não correspondendo ao modelo.

### <a name="are-extensions-run-again-when-an-existing-machine-is-service-healed-or-reimaged"></a>As extensões são executadas novamente quando uma máquina existente é curada ou reimagem?

Se um VM existente estiver curado de serviço, aparece como um reboot, e as extensões não são executadas novamente. Se um VM for reimaged, o processo é semelhante substituindo a unidade de SO com a imagem de origem. Qualquer especialização do modelo mais recente, como extensões, é novamente executada.

### <a name="how-do-i-join-a-virtual-machine-scale-set-to-an-active-directory-domain"></a>Como me junto a uma escala virtual de máquinas definida para um domínio de Diretório Ativo?

Para se juntar a uma escala de máquina virtual definida para um domínio de Diretório Ativo (AD), pode definir uma extensão.

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

### <a name="my-virtual-machine-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot"></a>A minha extensão de conjunto de máquinas virtuais está a tentar instalar algo que requer um reboot.

Se a extensão de conjunto de escala de máquina virtual estiver a tentar instalar algo que requer um reboot, pode utilizar a extensão de Configuração do Estado Desejada pela Automatização Azure (Automation DSC). Se o sistema operativo for o Windows Server 2012 R2, o Azure puxa a estrutura de gestão do Windows (WMF) 5.0 configuração, reinicia e continua com a configuração.

### <a name="how-do-i-turn-on-antimalware-in-my-virtual-machine-scale-set"></a>Como ligo antimalware na minha escala virtual?

Para ligar o antimalware no conjunto de escala de máquina virtual, utilize o seguinte exemplo PowerShell:

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

### <a name="how-do-i-execute-a-custom-script-thats-hosted-in-a-private-storage-account"></a>Como executo um guião personalizado que está hospedado numa conta de armazenamento privado?

Para executar um script personalizado que está hospedado numa conta de armazenamento privada, crie configurações protegidas com a chave e nome da conta de armazenamento. Para mais informações, consulte [a extensão do script personalizado](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/#template-example-for-a-windows-vm-with-protected-settings).

## <a name="passwords"></a>Palavras-passe

### <a name="how-do-i-reset-the-password-for-vms-in-my-virtual-machine-scale-set"></a>Como redefinir a palavra-passe para VMs no meu conjunto de máquinas virtuais?

Existem duas formas principais de alterar a palavra-passe para VMs em conjuntos de escala.

- Mude diretamente o modelo de conjunto de conjunto de máquinas virtuais. Disponível com API 2017-12-01 e mais tarde.

    Atualize as credenciais de administração diretamente no modelo de conjunto de escala (por exemplo, utilizando o Explorador de Recursos Azure, PowerShell ou CLI). Uma vez atualizado o conjunto de escala, todos os novos VMs têm as novas credenciais. Os VMexistentes só têm as novas credenciais se forem reimagemdas.

- Redefinir a palavra-passe utilizando as extensões de acesso VM.

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

## <a name="networking"></a>Redes

### <a name="is-it-possible-to-assign-a-network-security-group-nsg-to-a-scale-set-so-that-it-applies-to-all-the-vm-nics-in-the-set"></a>É possível atribuir um Grupo de Segurança da Rede (NSG) a um conjunto de escala, de modo a que se aplique a todos os NICs VM no conjunto?

Sim. Um Grupo de Segurança de Rede pode ser aplicado diretamente a uma escala definida, referenciando-a na secção de Configurações de Interface de rede do perfil da rede. Exemplo:

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

### <a name="how-do-i-do-a-vip-swap-for-virtual-machine-scale-sets-in-the-same-subscription-and-same-region"></a>Como faço uma troca VIP por conjuntos de escala de máquinas virtuais na mesma subscrição e mesma região?

Se tiver dois conjuntos de escala de máquina virtual com extremidades dianteiras do Azure Load Balancer, e estiverem na mesma subscrição e região, poderá desalocar os endereços IP públicos de cada um e atribuir ao outro. Ver [Troca VIP: Implantação azul-verde no Gestor de Recursos Azure,](https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/) por exemplo. Isto implica um atraso, embora, uma vez que os recursos são transferidos/atribuídos a nível da rede. Uma opção mais rápida é usar o Gateway de Aplicação Azure com duas piscinas de backend, e uma regra de encaminhamento. Em alternativa, poderá acolher a sua aplicação com [o serviço Azure App,](https://azure.microsoft.com/services/app-service/) que fornece suporte para uma rápida troca entre as faixas de treino e as ranhuras de produção.

### <a name="how-do-i-specify-a-range-of-private-ip-addresses-to-use-for-static-private-ip-address-allocation"></a>Como especifico uma série de endereços IP privados para utilizar para a atribuição de endereços IP privados estáticos?

Os endereços IP são selecionados a partir de uma sub-rede que especifica.

O método de atribuição de endereços IP de escala de máquina virtual é sempre "dinâmico", mas isso não significa que estes endereços IP possam mudar. Neste caso, "dinâmico" significa apenas que não especifica o endereço IP num pedido DE PUT. Especifique o conjunto estático utilizando a sub-rede.

### <a name="how-do-i-deploy-a-virtual-machine-scale-set-to-an-existing-azure-virtual-network"></a>Como posso implantar uma escala virtual de máquinas definida para uma rede virtual Azure existente?

Para implantar uma escala de máquina virtual definida para uma rede virtual Azure existente, consulte [a implantação de uma escala de máquina virtual definida para uma rede virtual existente.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet)

### <a name="can-i-use-scale-sets-with-accelerated-networking"></a>Posso usar conjuntos de escala com Networking Acelerado?

Sim. Para utilizar a rede acelerada, o conjunto permite acelerar a rede de redes verdadeiramente nas definições de Configurações de Interfacede configurações do conjunto de escala. Por exemplo

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

### <a name="how-can-i-configure-the-dns-servers-used-by-a-scale-set"></a>Como posso configurar os servidores DNS utilizados por um conjunto de escala?

Para criar um conjunto de escala de máquina virtual com uma configuração DNS personalizada, adicione um pacote DNsSettings JSON à secção de configurações de interface de rede de conjunto de escalaInterfaceS. Exemplo:

```json
    "dnsSettings":{
        "dnsServers":["10.0.0.6", "10.0.0.5"]
    }
```

### <a name="how-can-i-configure-a-scale-set-to-assign-a-public-ip-address-to-each-vm"></a>Como posso configurar uma escala definida para atribuir um endereço IP público a cada VM?

Para criar um conjunto de escala de máquina virtual que atribui um endereço IP público a cada VM, certifique-se de que a versão API do recurso Microsoft.Compute/virtualMachineScaleSets é 2017-03-30 e adicione um pacote _DeConfiguração de Configurações Públicas_ à secção ipConfiguras definida seletiva. Exemplo:

```json
    "publicipaddressconfiguration": {
        "name": "pub1",
        "properties": {
        "idleTimeoutInMinutes": 15
        }
    }
```

### <a name="can-i-configure-a-scale-set-to-work-with-multiple-application-gateways"></a>Posso configurar uma escala definida para funcionar com vários Gateways de Aplicação?

Sim. Pode adicionar os IDs de recurso para vários conjuntos de endereços de backend do Gateway de aplicação à lista de _aplicaçõesGatewayBackendAddressPools_ na secção _ipConfiguras_ do seu perfil de rede definido por escala.

## <a name="scale"></a>Escala

### <a name="in-what-case-would-i-create-a-virtual-machine-scale-set-with-fewer-than-two-vms"></a>Em que caso, criaria uma escala virtual de máquinas com menos de dois VMs?

Uma das razões para criar um conjunto de máquinas virtuais com menos de dois VMs seria utilizar as propriedades elásticas de um conjunto de escala de máquina virtual. Por exemplo, você poderia implementar um conjunto de máquinas virtuais com zero VMs para definir a sua infraestrutura sem pagar os custos de funcionamento do VM. Em seguida, quando estiver pronto para implantar VMs, aumente a "capacidade" da escala virtual da máquina definida para a contagem de instâncias de produção.

Outra razão pela qual você pode criar um conjunto de escala de máquina virtual com menos de dois VMs é se você está menos preocupado com a disponibilidade do que em usar um conjunto de disponibilidade com VMs discretos. Conjuntos de escala de máquina virtual dão-lhe uma maneira de trabalhar com unidades computacionais indiferenciadas que são fungíveis. Esta uniformidade é um diferenciador chave para conjuntos de escala de máquinavirtual versus conjuntos de disponibilidade. Muitas cargas de trabalho apátridas não rastreiam unidades individuais. Se a carga de trabalho diminuir, pode reduzir-se a uma unidade de cálculo e, em seguida, escalar para muitos quando a carga de trabalho aumentar.

### <a name="how-do-i-change-the-number-of-vms-in-a-virtual-machine-scale-set"></a>Como posso alterar o número de VMs num conjunto de máquinas virtuais?

Para alterar o número de VMs numa escala de máquina virtual definida no portal Azure, a partir da secção de propriedades de conjunto de máquinas virtuais, clique na lâmina "Escalar" e utilize a barra de slider.

### <a name="how-do-i-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>Como posso definir alertas personalizados para quando certos limiares são atingidos?

Tem alguma flexibilidade na forma como lida com alertas para limiares especificados. Por exemplo, pode definir webhooks personalizados. O exemplo webhook que se segue é de um modelo de Gestor de Recursos:

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

### <a name="can-i-move-a-scale-set-to-another-resource-group"></a>Posso mover uma escala definida para outro grupo de recursos?

Sim, pode mover recursos de escala para uma nova subscrição ou grupo de recursos.

### <a name="how-to-i-update-my-virtual-machine-scale-set-to-a-new-image-how-do-i-manage-patching"></a>Como atualizar a minha escala de máquina virtual definida para uma nova imagem? Como posso remendar?

Para atualizar a sua escala de máquina virtual definida para uma nova imagem e para gerir o patching, consulte [Atualização de um conjunto de escala](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set)de máquina virtual .

### <a name="can-i-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-i-want-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>Posso usar a operação de reimagem para repor um VM sem alterar a imagem? (Isto é, quero repor um VM em configurações de fábrica em vez de uma nova imagem.)

Sim, pode utilizar a operação de reimagem para repor um VM sem alterar a imagem. No entanto, se a sua escala `version = latest`de máquina virtual definir referências a uma `reimage`imagem de plataforma com , o seu VM pode atualizar para uma imagem de OS posterior quando ligar .

### <a name="is-it-possible-to-integrate-scale-sets-with-azure-monitor-logs"></a>É possível integrar conjuntos de escala com registos do Monitor Azure?

Sim, pode instalar a extensão do Monitor Azure nos VMs de conjunto de escala. Aqui está um exemplo do Azure CLI:

```azurecli
az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group Team-03 --vmss-name nt01 --settings "{'workspaceId': '<your workspace ID here>'}" --protected-settings "{'workspaceKey': '<your workspace key here'}"
```

Pode encontrar o espaço de trabalho necessárioId e workspaceKey no espaço de trabalho Log Analytics do portal Azure. Na página 'Visão Geral', clique no azulejo Definições. Clique no separador Fontes Conectadas na parte superior.

> [!NOTE]
> Se a _atualização_ do conjunto de escala estiver definida para Manual, terá de aplicar a extensão a todos os VMs definidos, ligando para a atualização dos mesmos. No CLI, estas seriam _az vmss update-instances_.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="how-do-i-turn-on-boot-diagnostics"></a>Como ligo os diagnósticos de botas?

Para ligar os diagnósticos de arranque, primeiro, criar uma conta de armazenamento. Em seguida, coloque este bloco JSON na sua escala virtual de escala de **máquinas virtualMachineProfile**, e atualize o conjunto de escala de máquina virtual:

```json
"diagnosticsProfile": {
    "bootDiagnostics": {
        "enabled": true,
        "storageUri": "http://yourstorageaccount.blob.core.windows.net"
    }
}
```

Quando um novo VM é criado, a propriedade InstanceView do VM mostra os detalhes para a imagem, e assim por diante. Segue-se um exemplo:

```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
}
```

## <a name="virtual-machine-properties"></a>Propriedades de máquinas virtuais

### <a name="how-do-i-get-property-information-for-each-vm-without-making-multiple-calls-for-example-how-would-i-get-the-fault-domain-for-each-of-the-100-vms-in-my-virtual-machine-scale-set"></a>Como posso obter informações de propriedade para cada VM sem fazer várias chamadas? Por exemplo, como obteria o domínio de falha para cada um dos 100 VMs no meu conjunto de máquinas virtuais?

Para obter informações sobre propriedades para cada VM sem fazer várias chamadas, pode ligar `ListVMInstanceViews` fazendo uma API `GET` REST sobre o seguinte recurso URI:

/subscrições/<subscription_id>/recursosGroups/<resource_group_name>/providers/Microsoft.Compute/virtualMachineScaleSets/<scaleset_name>/virtualMachines?$expand=instânciaVer&$select=instânciaVer

### <a name="can-i-pass-different-extension-arguments-to-different-vms-in-a-virtual-machine-scale-set"></a>Posso passar diferentes argumentos de extensão para diferentes VMs num conjunto de escala de máquina virtual?

Não, não pode passar diferentes argumentos de extensão para diferentes VMs num conjunto de escala de máquina virtual. No entanto, as extensões podem atuar com base nas propriedades únicas do VM em que estão a funcionar, como no nome da máquina. As extensões também podem consultar http://169.254.169.254 metadados de instância para obter mais informações sobre o VM.

### <a name="why-are-there-gaps-between-my-virtual-machine-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>Porque é que existem lacunas entre os nomes das máquinas vm da minha escala virtual e os IDs VM? Por exemplo: 0, 1, 3...

Existem lacunas entre os nomes das máquinas vM de escala virtual e os IDs VM porque a sua propriedade de **sobreoferta** de escala de máquina virtual está definida para o valor padrão de **verdadeiro**. Se o excesso de oferta for definido para **ser verdade,** são criados mais VMs do que solicitados. Os VMextra são então eliminados. Neste caso, ganha-se uma maior fiabilidade de implantação, mas à custa de regras contíguas de nomeação e tradução contígua de endereços de rede (NAT).

Você pode definir esta propriedade como **falsa.** Para pequenos conjuntos de escala de máquinavirtual, isto não afeta significativamente a fiabilidade da implementação.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-virtual-machine-scale-set-and-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>Qual é a diferença entre apagar um VM num conjunto de máquinas virtuais e fazer a localização do VM? Quando devo escolher um em vez do outro?

A principal diferença entre eliminar um VM num conjunto de escala de `deallocate` máquina virtual e fazer a localização do VM é que não apaga os discos rígidos virtuais (VHDs). Existem custos de `stop deallocate`armazenamento associados ao funcionamento . Pode usar uma ou outra por uma das seguintes razões:

- Queres parar de pagar os custos da computação, mas queres manter o estado do disco dos VMs.
- Você quer iniciar um conjunto de VMs mais rapidamente do que você poderia escalar um conjunto de escala de máquina virtual.
  - Relacionado com este cenário, pode ter criado o seu próprio motor de escala automática e querer uma escala de ponta a ponta mais rápida.
- Tem um conjunto de escala de máquina virtual que é distribuído de forma desigual em domínios de falha ou domínios de atualização. Isto pode ser porque você apagou seletivamente VMs, ou porque os VMs foram eliminados após o excesso de provisionamento. A `stop deallocate` execução seguida no `start` conjunto de escala de máquina virtual distribui uniformemente os VMs através de domínios de falha ou domínios de atualização.

### <a name="how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance"></a>Como posso tirar uma foto de uma instância de escala de máquina virtual?
Crie um instantâneo a partir de um conjunto de escala de máquina virtual.

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
