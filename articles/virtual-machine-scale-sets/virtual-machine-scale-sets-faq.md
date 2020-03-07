---
title: FAQs de conjuntos de dimensionamento de máquina virtual do Azure
description: Obtenha respostas às perguntas mais frequentes sobre conjuntos de escala de máquinas virtuais em Azure.
author: mayanknayar
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: manayar
ms.openlocfilehash: 222f26febb7b14c627307295a8cdd68a17694d03
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78394641"
---
# <a name="azure-virtual-machine-scale-sets-faqs"></a>FAQs de conjuntos de dimensionamento de máquina virtual do Azure

Obtenha respostas às perguntas mais frequentes sobre os conjuntos de dimensionamento de máquinas virtuais no Azure.

## <a name="top-frequently-asked-questions-for-scale-sets"></a>Principais perguntas mais frequentes sobre os conjuntos de dimensionamento

### <a name="how-many-vms-can-i-have-in-a-scale-set"></a>Quantas VMs posso ter num conjunto de dimensionamento?

Um conjunto de dimensionamento pode ter as VMs de 0 a 1000 com base nas imagens da plataforma ou VMs de 0 e 600 com base em imagens personalizadas.

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

Criar e capturar uma imagem de VM e, em seguida, utilizá-la como a origem para o conjunto de dimensionamento. Para um tutorial sobre como criar e usar uma imagem VM personalizada, pode utilizar o [Azure CLI](tutorial-use-custom-image-cli.md) ou [Azure PowerShell](tutorial-use-custom-image-powershell.md)

### <a name="if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed"></a>Se reduzir a capacidade do conjunto de dimensionamento de 20 para 15, que VMs são removidas?

As máquinas virtuais são removidas do conjunto de dimensionamento de forma uniforme entre domínios de atualização e domínios de falha, para maximizar a disponibilidade. São removidas primeiro as VMs com os IDs mais altos.

### <a name="what-if-i-then-increase-the-capacity-from-15-to-18"></a>E se, depois, aumentar a capacidade de 15 para 18?

Se aumentar a capacidade para 18, então, são criadas três VMs novas. Sempre que isso acontecer, o ID da instância da VM é aumentado a partir do valor mais alto anterior (por exemplo, 20, 21, 22). As VMs são balanceadas entre os domínios de falhas e os domínios de atualização.

### <a name="when-im-using-multiple-extensions-in-a-scale-set-can-i-enforce-an-execution-sequence"></a>Se utilizar várias extensões num conjunto de dimensionamento, posso forçar uma sequência de execução?

Sim, pode utilizar [a seqüência](virtual-machine-scale-sets-extension-sequencing.md)de extensão definida pela balança.

### <a name="do-scale-sets-work-with-azure-availability-sets"></a>Os conjuntos de dimensionamento funcionam com os conjuntos de disponibilidade do Azure?

Um conjunto de escala regional (não zonal) utiliza grupos de *colocação,* que funcionam como um conjunto de disponibilidade implícita com cinco domínios de falha e cinco domínios de atualização. Os conjuntos de dimensionamento de mais de 100 VMs abrangem vários grupos de colocação. Para obter mais informações sobre os grupos de posicionamento, veja [Trabalhar com conjuntos de dimensionamento de máquinas virtuais de grande escala](virtual-machine-scale-sets-placement-groups.md). Um conjunto de disponibilidade de VMs pode existir na mesma rede virtual como um conjunto de dimensionamento de VMs. Uma configuração comum é colocar as VMs de nó de controlo (que, muitas vezes, requerem uma configuração exclusiva) num conjunto de disponibilidade e os nós de dados no conjunto de dimensionamento.

### <a name="do-scale-sets-work-with-azure-availability-zones"></a>Conjuntos de dimensionamento funcionam com zonas de disponibilidade do Azure?

Sim! Para mais informações, consulte a zona definida em [escala doc](./virtual-machine-scale-sets-use-availability-zones.md).


## <a name="autoscale"></a>Dimensionamento Automático

### <a name="what-are-best-practices-for-azure-autoscale"></a>Quais são as práticas recomendadas para dimensionamento automático do Azure?

Para obter as melhores práticas para autoescala, consulte [as melhores práticas para autoscalcificar máquinas virtuais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-best-practices).

### <a name="where-do-i-find-metric-names-for-autoscaling-that-uses-host-based-metrics"></a>Onde posso encontrar os nomes de métricas para o dimensionamento automático que utiliza as métricas baseadas no anfitrião?

Para nomes métricos para autoscalcificação que utilizam métricas baseadas em hospedeiros, consulte [métricas suportadas com o Monitor Azure](https://azure.microsoft.com/documentation/articles/monitoring-supported-metrics/).

### <a name="are-there-any-examples-of-autoscaling-based-on-an-azure-service-bus-topic-and-queue-length"></a>Existem exemplos do dimensionamento automático com base num comprimento de fila e tópico do Service bus do Azure?

Sim. Por exemplo, de autoscalcificação com base num tópico de ônibus de serviço Azure e comprimento de fila, consulte [as métricas comuns do Azure Monitor.](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/)

Para uma fila do Service Bus, utilize o seguinte JSON:

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

Substitua os valores de exemplo em seu recurso Uniform Resource Identifiers (URIs).


### <a name="should-i-autoscale-by-using-host-based-metrics-or-a-diagnostics-extension"></a>Deve, dimensionamento automático com as métricas baseadas no anfitrião ou uma extensão de diagnóstico?

Pode criar uma definição de dimensionamento automático numa VM para utilizar métricas ao nível do anfitrião ou as métricas de com base no sistema operacional convidado.

Para obter uma lista de métricas suportadas, consulte o [Azure Monitor a automatizar métricas comuns](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-common-metrics).

Para obter uma amostra completa para conjuntos de escala de máquina virtual, consulte a configuração avançada de escala automática utilizando modelos de Gestor de [Recursos para conjuntos de escala](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets)de máquina virtual .

Este exemplo utiliza a métrica de CPU de ao nível do anfitrião e uma métrica de contagem de mensagens.



### <a name="how-do-i-set-alert-rules-on-a-virtual-machine-scale-set"></a>Como posso definir regras de alerta num conjunto de dimensionamento de máquinas virtuais?

Pode criar alertas em métricas para conjuntos de dimensionamento de máquinas virtuais através do PowerShell ou da CLI do Azure. Para mais informações, consulte [as amostras de arranque rápido do Azure Monitor PowerShell](https://azure.microsoft.com/documentation/articles/insights-powershell-samples/#create-alert-rules) e as [amostras de arranque rápido CLI](https://azure.microsoft.com/documentation/articles/insights-cli-samples/#work-with-alerts)da plataforma transversal Azure Monitor .

TargetResourceId do conjunto de dimensionamento de máquina virtual tem o seguinte aspeto:

/subscriptions/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname

Pode escolher qualquer contador de desempenho da VM como a métrica para definir um alerta para. Para obter mais informações, consulte as métricas do Os do Guest PARA Os de [Recursos Baseados em Windows VMs](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-resource-manager-based-windows-vms) e [métricas de Os convidados para VMs Linux](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-linux-vms) no artigo de [métricas comuns do Monitor Descalcificação Azure Monitor.](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/)

### <a name="how-do-i-set-up-autoscale-on-a-virtual-machine-scale-set-by-using-powershell"></a>Como configurar dimensionamento automático num conjunto de dimensionamento com o PowerShell?

Para configurar a escala automática numa escala de máquina virtual definida utilizando o PowerShell, consulte [automaticamente uma balança](tutorial-autoscale-powershell.md)de escala virtual . Também pode configurar a escala automática com os modelos [Azure CLI](tutorial-autoscale-cli.md) e [Azure](tutorial-autoscale-template.md)


### <a name="if-i-have-stopped-deallocated-a-vm-is-that-vm-started-as-part-of-an-autoscale-operation"></a>Se o ter parado (desalocado) é de uma VM, VM iniciado como parte de uma operação de dimensionamento automático?

Não. Se as regras de dimensionamento automático exigirem mais instâncias de VMS como parte de um conjunto de dimensionamento, é criada uma nova instância VM. Instâncias de VM que estão a ser paradas (desalocadas) não são iniciadas como parte de um evento de dimensionamento automático. No entanto, essas parada (desalocadas) de VMs podem ser eliminadas como parte de um evento de dimensionamento automático da mesma forma que qualquer instância VM pode ser eliminada com base na ordem de instância de VM de aumenta o número de instâncias, de ID.



## <a name="certificates"></a>Certificados

### <a name="how-do-i-securely-ship-a-certificate-to-the-vm"></a>Como posso enviar em segurança um certificado para a VM?

Enviar em segurança um certificado à VM, pode instalar um certificado de cliente diretamente para um arquivo de certificados do Windows do Cofre de chaves do cliente.

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

```bash
az sf cluster create -h
```

Os certificados auto-assinados não podem ser utilizados para a confiança distribuída fornecida por uma Autoridade de Certificados, não devendo ser utilizados para qualquer Cluster de Tecidos de Serviço destinado a acolher soluções de produção empresarial; para orientação adicional de segurança de tecido de serviço, reveja as [melhores práticas](https://docs.microsoft.com/azure/security/fundamentals/service-fabric-best-practices) de segurança do tecido do serviço Azure e os cenários de segurança do cluster de tecido de [serviço.](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/)

### <a name="can-i-specify-an-ssh-key-pair-to-use-for-ssh-authentication-with-a-linux-virtual-machine-scale-set-from-a-resource-manager-template"></a>Pode especificar um par de chaves SSH para utilizar para autenticação de SSH com um dimensionamento de máquinas virtuais do Linux definido a partir de um modelo do Resource Manager?

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

### <a name="how-do-i-remove-deprecated-certificates"></a>Como posso remover certificados preteridos?

Para remover certificados preteridos, remova o antigo certificado na lista de certificados do cofre. Deixe todos os certificados que pretende que permanecem no seu computador na lista. Isto não remove o certificado de todas as suas VMs. Ele também não adiciona o certificado para novas VMs que são criadas no conjunto de dimensionamento de máquina virtual.

Para remover o certificado dos VMs existentes, utilize uma extensão de script personalizada para remover manualmente os certificados da sua loja de certificados.

### <a name="how-do-i-inject-an-existing-ssh-public-key-into-the-virtual-machine-scale-set-ssh-layer-during-provisioning"></a>Como inserir uma chave pública de SSH existente para a camada SSH de conjunto de dimensionamento de máquina virtual durante o aprovisionamento?

Se está a fornecer as VMs apenas com uma chave SSH pública, não precisa colocar as chaves públicas no Cofre de chaves. Chaves públicas não são secretas.

Pode fornecer as chaves públicas SSH em texto simples quando criar uma VM do Linux:

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
SSH | Não | Coleção | Especifica a configuração de chave SSH para um sistema operacional do Linux
caminho | Sim | String | Especifica o caminho de ficheiro do Linux em que as chaves SSH ou o certificado deve estar localizado
keyData | Sim | String | Especifica uma chave pública de SSH codificada em base64

Por exemplo, consulte o modelo de [arranque rápido GitHub de 101-vm-sshkey](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

### <a name="when-i-run-update-azvmss-after-adding-more-than-one-certificate-from-the-same-key-vault-i-see-the-following-message"></a>Quando corro `Update-AzVmss` depois de adicionar mais de um certificado do mesmo cofre, vejo a seguinte mensagem:

>Update-AzVmss: List secret contém instâncias repetidas de /subscrições/\<my-subscription-id>/resourceGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev, o que é proibido.

Isto pode acontecer se tentar voltar a adicionar o mesmo cofre em vez de utilizar um certificado do cofre novo para o Cofre de origem existente. O comando `Add-AzVmssSecret` não funciona corretamente se estiver a adicionar segredos adicionais.

Para adicionar mais segredos do Cofre de chave do mesmo, atualize a lista de $vmss.properties.osProfile.secrets[0].vaultCertificates.

Para a estrutura de entrada esperada, consulte Criar ou atualizar um conjunto de [máquinas virtuais](https://msdn.microsoft.com/library/azure/mt589035.aspx).

Encontre o segredo no objeto de conjunto de dimensionamento da máquina virtual que está no Cofre de chaves. Em seguida, adicione a referência de certificado (o URL e o nome de arquivo de segredos) para a lista associada ao cofre.

> [!NOTE]
> Atualmente, não é possível remover certificados de VMs ao utilizar a API de conjunto de dimensionamento de máquina virtual.
>

Novas VMs não terão o certificado antigo. No entanto, as VMs que tenham o certificado e que já estão implementado terão o certificado antigo.

### <a name="can-i-push-certificates-to-the-virtual-machine-scale-set-without-providing-the-password-when-the-certificate-is-in-the-secret-store"></a>Pode emitir certificados para o conjunto sem fornecer a palavra-passe, quando o certificado está no arquivo de segredos de dimensionamento de máquina virtual?

Não é necessário para palavras-passe de codificar em scripts. Pode obter dinamicamente as palavras-passe com as permissões que utiliza para executar o script de implementação. Se tiver um script que mova um certificado do cofre da loja secreta, a loja secreta `get certificate` comando também produz a palavra-passe do ficheiro .pfx.

### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-for-a-virtual-machine-scale-set-work-why-do-i-need-the-sourcevault-value-when-i-have-to-specify-the-absolute-uri-for-a-certificate-by-using-the-certificateurl-property"></a>Como é que a propriedade de segredos de virtualMachineProfile.osProfile para um dimensionamento de máquinas virtuais Definir trabalho? Por que motivo é necessário o valor de sourceVault quando tenho de especificar o URI absoluto para um certificado utilizando a propriedade certificateUrl?

Uma referência de certificado de gestão remota do Windows (WinRM) tem de estar presente na propriedade segredos do perfil de SO.

O objetivo de indicar o Cofre de origem é para impor políticas de (ACL lista) de controlo de acesso que existem no modelo de serviço Cloud do Azure de um utilizador. Se o Cofre de origem não for especificado, os utilizadores que não tem permissões para implementar ou aceder aos segredos para um cofre de chaves seria capazes de por meio de um fornecedor de recursos de computação (CRP). ACLs existem mesmo para recursos que não existem.

Se fornecer um ID de Cofre de origem incorreto, mas um URL do Cofre de chaves válida, é comunicado um erro quando consultar a operação.

### <a name="if-i-add-secrets-to-an-existing-virtual-machine-scale-set-are-the-secrets-injected-into-existing-vms-or-only-into-new-ones"></a>Se eu adicionar segredos um existente já o dimensionamento de máquinas virtuais definido, são segredos injetados em VMs existentes, ou apenas para novas?

Certificados são adicionados a todas as suas VMs, até mesmo previamente existentes. Se a sua atualização de escalade máquina virtual configurar a propriedade Está definida manualmente, o certificado é adicionado ao VM quando executa uma atualização manual no VM.

### <a name="where-do-i-put-certificates-for-linux-vms"></a>Onde coloco certificados para VMs do Linux?

Para aprender a implementar certificados para VMs Linux, consulte [A utilização de certificados para VMs a partir de um cofre de chaves gerido pelo cliente](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/).

### <a name="how-do-i-add-a-new-vault-certificate-to-a-new-certificate-object"></a>Como posso adicionar um certificado do cofre novo a um novo objeto de certificado?

Para adicionar um certificado do cofre a um segredo existente, veja o seguinte exemplo do PowerShell. Utilize apenas um objeto secreto.

```powershell
$newVaultCertificate = New-AzVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809

$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)

Update-AzVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```

### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>O que acontece aos certificados se recriar a imagem de uma VM?

Se recriar a imagem de uma VM, os certificados são eliminados. Recriar a imagem de disco de eliminações o SO completo.

### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>O que acontece se eliminar um certificado do Cofre de chaves?

Se o segredo for apagado do cofre da chave, e correr`stop deallocate` para todos os seus VMs e depois reiniciá-los novamente, encontra-se um fracasso. A falha ocorre porque o CRP precisa obter os segredos do Cofre de chaves, mas isso não é possível. Neste cenário, pode eliminar os certificados do modelo de conjunto de dimensionamento de máquina virtual.

O componente de CRP não persiste segredos do cliente. Se executar `stop deallocate` para todos os VMs no conjunto de escala de máquina virtual, a cache é eliminada. Neste cenário, os segredos são recuperados do Cofre de chaves.

Não enfrenta esse problema ao aumentar horizontalmente, porque existe uma cópia em cache do segredo do Azure Service Fabric (no modelo de recursos de infraestrutura único inquilino).

### <a name="why-do-i-have-to-specify-the-certificate-version-when-i-use-key-vault"></a>Por que motivo tenho de especificar a versão de certificado, ao utilizar o Key Vault?

O objetivo do requisito de Key Vault para especificar a versão de certificado é para torná-lo claro que o utilizador que certificado é implementado nas suas VMs.

Se cria uma VM e, em seguida, atualize o seu segredo no Cofre de chaves, o novo certificado não é transferido para as suas VMs. Mas as suas VMs são apresentados fazer referência a ela e novas VMs obtém o novo segredo. Para evitar isto, é necessário para fazer referência a uma versão do segredo.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-for-deploying-these-certificates-to-a-virtual-machine-scale-set"></a>Minha equipe funciona com vários certificados que são distribuídos para nós como. cer chaves públicas. O que é definida a abordagem recomendada para implementar estes certificados para um dimensionamento de máquinas virtuais?

Para implementar. cer conjunto de chaves públicas para um dimensionamento de máquinas virtuais, pode gerar um ficheiro. pfx que contém apenas os ficheiros. cer. Para isso, use `X509ContentType = Pfx`. Por exemplo, carregar o ficheiro. cer como um objeto de x509Certificate2 em c# ou do PowerShell e, em seguida, chamar o método.

Para mais informações, consulte [X509Certificate.Export Method (X509ContentType, String)](https://msdn.microsoft.com/library/24ww6yzk(v=vs.110.aspx)).

### <a name="how-do-i-pass-in-certificates-as-base64-strings"></a>Como passo em certificados como cordas base64?

Para emular a aprovação num certificado como uma cadeia de caracteres em base64, é possível extrair o URL com a versão mais recente num modelo do Resource Manager. Inclua a seguinte propriedade JSON no seu modelo de Gestor de recursos:

```json
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```

### <a name="do-i-have-to-wrap-certificates-in-json-objects-in-key-vaults"></a>É necessário que encapsular os certificados em objetos JSON em cofres de chaves?

Em VMs e conjuntos de dimensionamento de máquina virtual, os certificados devem ser encapsulados em objetos JSON.

Também suportamos o tipo de conteúdo application/x-pkcs12.

Atualmente não suportamos ficheiros. cer. Para utilizar ficheiros. cer, exportá-las em contentores. pfx.



## <a name="compliance-and-security"></a>Conformidade e segurança

### <a name="are-virtual-machine-scale-sets-pci-compliant"></a>Estão em conformidade com PCI de conjuntos de dimensionamento de máquina virtual?

Os conjunto de dimensionamento de máquinas virtuais são uma fina camada de API sobreposta ao CRP. Os dois componentes fazem parte da plataforma de computação da árvore de serviço do Azure.

Numa perspetiva de conformidade, os conjuntos de dimensionamento de máquinas virtuais são uma parte fundamental da plataforma de computação do Azure. Partilham uma equipa, ferramentas, processos, metodologia de implementação, controlos de segurança, compilação just-in-time (JIT), monitorização, alertas e assim sucessivamente, com o próprio CRP. Os conjuntos de dimensionamento de máquinas virtuais estão conformes ao Payment Card Industry (PCI) porque o CRP faz parte do atestado da atual Norma de Segurança dos Dados (DSS) da PCI.

Para obter mais informações, veja o [Centro de Fidedignidade da Microsoft](https://www.microsoft.com/TrustCenter/Compliance/PCI).

### <a name="does-managed-identities-for-azure-resources-work-with-virtual-machine-scale-sets"></a>As [identidades geridas para os recursos do Azure](https://docs.microsoft.com/azure/active-directory/msi-overview) funcionam com conjuntos de escala de máquinas virtuais?

Sim. Pode ver alguns modelos de MSI exemplo em modelos Azure Quickstart para [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi) e [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi).

## <a name="deleting"></a>Apagando 

### <a name="will-the-locks-i-set-in-place-on-virtual-machine-scale-set-instances-be-respected-when-deleting-instances"></a>As fechaduras que coloquei em instâncias de conjunto de máquinas virtuais serão respeitadas ao apagar casos?

No Portal Azure, tem a capacidade de eliminar uma instância individual ou apagar a granel selecionando várias instâncias. Se tentar eliminar uma única instância que tenha um cadeado no lugar, o bloqueio é respeitado e não poderá apagar a instância. No entanto, se selecionar várias instâncias em massa e qualquer uma dessas instâncias tiver um bloqueio no lugar, o bloqueio(s) não será respeitado e todas as instâncias selecionadas serão eliminadas. 
 
No Azure CLI, só tem a capacidade de eliminar uma instância individual. Se tentar eliminar uma única instância que tenha um cadeado no lugar, o bloqueio é respeitado e não poderá eliminar essa instância. 

## <a name="extensions"></a>Extensões

### <a name="how-do-i-delete-a-virtual-machine-scale-set-extension"></a>Como faço para excluir uma extensão de conjunto de dimensionamento da máquina virtual?

Para eliminar uma extensão de conjunto de dimensionamento da máquina virtual, utilize o seguinte exemplo de PowerShell:

```powershell
$vmss = Get-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName"

$vmss=Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```

Pode encontrar o valor do nome de extensão em `$vmss`.

### <a name="is-there-a-virtual-machine-scale-set-template-example-that-integrates-with-azure-monitor-logs"></a>Existe um exemplo de modelo de modelo de escala de máquina virtual que se integra com registos do Monitor Azure?

Para um exemplo de modelo de modelo de conjunto de escala de máquina virtual que se integra com registos do Monitor Azure, consulte o segundo exemplo em implementar um cluster de tecido de [serviço Azure e permitir a monitorização utilizando registos do Monitor Azure](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric).

### <a name="how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set"></a>Como adicionar uma extensão para todas as VMs no meu conjunto de dimensionamento de máquina virtual?

Se a política de atualização estiver definida para **automática,** reimplantando o modelo com as novas propriedades de extensão atualiza todos os VMs.

Se a política de atualização estiver definida para **manual,** atualize primeiro a extensão e, em seguida, atualize manualmente todas as instâncias dos seus VMs.

### <a name="if-the-extensions-associated-with-an-existing-virtual-machine-scale-set-are-updated-are-existing-vms-affected"></a>Se as extensões associadas um conjunto de dimensionamento de máquina virtual existente forem atualizadas, existentes VMs afetadas?

Se a definição de extensão no modelo de conjunto de escala de máquina virtual for atualizada e a propriedade de actualizaçãoPolítica for definida como **automática,** atualiza os VMs. Se a propriedade upgradePolítica for definida para **manual**, as extensões são sinalizadas como não correspondendo ao modelo.

### <a name="are-extensions-run-again-when-an-existing-machine-is-service-healed-or-reimaged"></a>As extensões são executadas novamente quando uma máquina existente é curada ou reimagem?

Se um VM existente estiver curado de serviço, aparece como um reboot, e as extensões não são executadas novamente. Se um VM for reimaged, o processo é semelhante substituindo a unidade de SO com a imagem de origem. Qualquer especialização do modelo mais recente, como extensões, é novamente executada.

### <a name="how-do-i-join-a-virtual-machine-scale-set-to-an-active-directory-domain"></a>Como posso associar um conjunto a um domínio do Active Directory de dimensionamento de máquina virtual?

Para associar um conjunto de dimensionamento a um domínio do Active Directory (AD), pode definir uma extensão.

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

### <a name="my-virtual-machine-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot"></a>Minha extensão de conjunto de dimensionamento da máquina virtual está a tentar instalar alguma coisa que requer uma reinicialização.

Se a sua extensão de conjunto de dimensionamento de máquina virtual está a tentar instalar alguma coisa que requer uma reinicialização, pode utilizar a extensão de Desired State Configuration do automatização de Azure (DSC de automatização). Se o sistema operativo Windows Server 2012 R2, o Azure obtém a configuração do Windows Management Framework (WMF) 5.0, reinícios e, em seguida, continua com a configuração.

### <a name="how-do-i-turn-on-antimalware-in-my-virtual-machine-scale-set"></a>Como posso ativá antimalware no meu conjunto de dimensionamento de máquina virtual?

Para ativar o antimalware em seu conjunto de dimensionamento de máquina virtual, utilize o seguinte exemplo de PowerShell:

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

Para executar um script personalizado que está alojado numa conta de armazenamento privado, configure definições protegidas com o nome e chave da conta de armazenamento. Para mais informações, consulte [a extensão do script personalizado](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/#template-example-for-a-windows-vm-with-protected-settings).

## <a name="passwords"></a>Palavras-passe

### <a name="how-do-i-reset-the-password-for-vms-in-my-virtual-machine-scale-set"></a>Como repor a palavra-passe para as VMs no meu conjunto de dimensionamento de máquina virtual?

Existem duas formas de alterar a palavra-passe para as VMs em conjuntos de dimensionamento.

- Altere o modelo de conjunto de dimensionamento de máquina virtual diretamente. Disponível com API 2017-12-01 e mais tarde.

    Atualize as credenciais de administrador diretamente no modelo de conjunto de dimensionamento (por exemplo utilizando o Explorador de recursos do Azure, o PowerShell ou a CLI). Assim que o conjunto de dimensionamento for atualizada, todas as novas VMs têm as novas credenciais. As VMs existentes têm apenas as novas credenciais se eles são a recriar a imagem.

- Repor a palavra-passe usando as extensões de acesso VM.

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

### <a name="is-it-possible-to-assign-a-network-security-group-nsg-to-a-scale-set-so-that-it-applies-to-all-the-vm-nics-in-the-set"></a>É possível atribuir um grupo de segurança de rede (NSG) para um conjunto de dimensionamento, de modo a que se aplique a todos os NICs de VM no conjunto de?

Sim. Um grupo de segurança de rede podem ser aplicado diretamente para fazer referência a ele na secção networkInterfaceConfigurations do perfil de rede de conjunto de dimensionamento. Exemplo:

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

### <a name="how-do-i-do-a-vip-swap-for-virtual-machine-scale-sets-in-the-same-subscription-and-same-region"></a>Como posso fazer uma alternância de VIP para conjuntos de dimensionamento de máquinas virtuais na mesma subscrição e na mesma região?

Se tiver dois conjuntos de dimensionamento de máquina virtual com o front-ends do Balanceador de carga do Azure e estão na mesma subscrição e região, foi possível desalocar os endereços IP públicos de cada um deles e atribuir a si. Ver [Troca VIP: Implantação azul-verde no Gestor de Recursos Azure,](https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/) por exemplo. Isso implica um atraso porém os recursos estejam desalocada/alocados na rede de nível. Uma opção mais rápida é usar o Gateway de aplicação do Azure com dois conjuntos de back-end e uma regra de roteamento. Em alternativa, poderá acolher a sua aplicação com [o serviço Azure App,](https://azure.microsoft.com/services/app-service/) que fornece suporte para uma rápida troca entre as faixas de treino e as ranhuras de produção.

### <a name="how-do-i-specify-a-range-of-private-ip-addresses-to-use-for-static-private-ip-address-allocation"></a>Como posso especificar um intervalo de endereços IP privados a utilizar para a alocação de endereço IP privada estático?

Endereços IP são selecionados a partir de uma sub-rede que especificar.

O método de alocação de endereços IP de conjunto de dimensionamento de máquina virtual é sempre "dynamic", mas isso não significa que estes endereços IP podem alterar. Neste caso, "dynamic" apenas significa que não especificar o endereço IP num pedido PUT. Especifique o estático definido com a sub-rede.

### <a name="how-do-i-deploy-a-virtual-machine-scale-set-to-an-existing-azure-virtual-network"></a>Como posso implementar um conjunto a uma rede virtual do Azure existente de dimensionamento de máquina virtual?

Para implantar uma escala de máquina virtual definida para uma rede virtual Azure existente, consulte [a implantação de uma escala de máquina virtual definida para uma rede virtual existente.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet)

### <a name="can-i-use-scale-sets-with-accelerated-networking"></a>Pode utilizar os conjuntos de dimensionamento com redes aceleradas?

Sim. Para utilizar redes aceleradas, defina enableAcceleratedNetworking como true no seu dimensionamento definições de networkInterfaceConfigurations do conjunto. Por exemplo
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

### <a name="how-can-i-configure-the-dns-servers-used-by-a-scale-set"></a>Como posso configurar os servidores DNS utilizados por um conjunto de dimensionamento?

Para criar um conjunto de dimensionamento com uma configuração DNS personalizada, adicione um pacote JSON dnsSettings à secção networkInterfaceConfigurations do conjunto de dimensionamento. Exemplo:
```json
    "dnsSettings":{
        "dnsServers":["10.0.0.6", "10.0.0.5"]
    }
```

### <a name="how-can-i-configure-a-scale-set-to-assign-a-public-ip-address-to-each-vm"></a>Como posso configurar um conjunto de dimensionamento para atribuir um endereço IP público a cada VM?

Para criar um conjunto de escala de máquina virtual que atribui um endereço IP público a cada VM, certifique-se de que a versão API do recurso Microsoft.Compute/virtualMachineScaleSets é 2017-03-30 e adicione um pacote _DeConfiguração de Configurações Públicas_ à secção ipConfiguras definida seletiva. Exemplo:

```json
    "publicipaddressconfiguration": {
        "name": "pub1",
        "properties": {
        "idleTimeoutInMinutes": 15
        }
    }
```

### <a name="can-i-configure-a-scale-set-to-work-with-multiple-application-gateways"></a>Pode configurar um conjunto de dimensionamento para trabalhar com vários Gateways de aplicação?

Sim. Pode adicionar os IDs de recurso para vários conjuntos de endereços de backend do Gateway de aplicação à lista de _aplicaçõesGatewayBackendAddressPools_ na secção _ipConfiguras_ do seu perfil de rede definido por escala.

## <a name="scale"></a>Escala

### <a name="in-what-case-would-i-create-a-virtual-machine-scale-set-with-fewer-than-two-vms"></a>O que caso eu criaria um conjunto de dimensionamento com menos de duas VMs?

Uma das razões para criar um conjunto de dimensionamento com menos de duas VMs poderia ser utilizar as propriedades elásticas de um conjunto de dimensionamento de máquina virtual. Por exemplo, pode implementar um conjunto de dimensionamento com zero VMs para definir a infraestrutura sem pagar custos de execução de VM. Em seguida, quando estiver pronto para implementar VMs, aumente a "capacidade" de dimensionamento da máquina virtual definida como a contagem de instâncias de produção.

Outro motivo, que poderá criar um conjunto de dimensionamento com menos de duas VMs é se está preocupado menos com disponibilidade de sessão com um conjunto de disponibilidade com VMs discretas. Os conjuntos de dimensionamento de máquinas virtuais dão-lhe uma forma de trabalhar com unidades de computação indiferenciadas que são intermutáveis. Esta uniformidade é um diferenciador fundamental para conjuntos de dimensionamento de máquinas virtuais em comparação com os conjuntos de disponibilidade. Muitas cargas de trabalho sem monitorização de estado não controlam unidades individuais. Se a diminuir a carga de trabalho, pode reduzir verticalmente para uma unidade de computação e, em seguida, aumente verticalmente para muitos quando a carga de trabalho aumenta.

### <a name="how-do-i-change-the-number-of-vms-in-a-virtual-machine-scale-set"></a>Como posso alterar o número de VMs num conjunto de dimensionamento de máquina virtual?

Para alterar o número de VMs num conjunto de dimensionamento no portal do Azure, a partir de dimensionamento de máquina virtual secção de propriedades do conjunto, clique no painel do "Dimensionamento" e utilize a barra de controlo de deslize.

### <a name="how-do-i-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>Como posso definir alertas personalizados para quando determinados limites são atingidos?

Tem alguma flexibilidade em como lidar com alertas para limites especificados. Por exemplo, pode definir webhooks personalizados. O seguinte exemplo de webhook é a partir de um modelo do Resource Manager:

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


## <a name="patching-and-operations"></a>A aplicação de patches e de operações

### <a name="can-i-create-a-scale-set-in-an-existing-resource-group"></a>Posso criar uma escala definida num grupo de recursos existente?

Sim, pode criar uma escala definida num grupo de recursos existente.

### <a name="can-i-move-a-scale-set-to-another-resource-group"></a>Posso mover uma escala definida para outro grupo de recursos?

Sim, pode mover recursos do conjunto de dimensionamento para uma nova subscrição ou grupo de recursos.

### <a name="how-to-i-update-my-virtual-machine-scale-set-to-a-new-image-how-do-i-manage-patching"></a>Como a que atualizo meu conjunto de dimensionamento para uma nova imagem? Como gerir a aplicação de patches?

Para atualizar a sua escala de máquina virtual definida para uma nova imagem e para gerir o patching, consulte [Atualização de um conjunto de escala](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set)de máquina virtual .

### <a name="can-i-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-i-want-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>Pode utilizar a operação de recriação de imagem para repor a uma VM sem alterar a imagem? (Ou seja, quero que a reposição de uma VM para as definições de fábrica, em vez de uma nova imagem.)

Sim, pode utilizar a operação de recriação de imagem para repor a uma VM sem alterar a imagem. No entanto, se a sua escala de máquina virtual definir referências a uma imagem de plataforma com `version = latest`, o seu VM pode atualizar para uma imagem posterior do OS quando chamar `reimage`.

### <a name="is-it-possible-to-integrate-scale-sets-with-azure-monitor-logs"></a>É possível integrar conjuntos de escala com registos do Monitor Azure?

Sim, pode instalar a extensão do Monitor Azure nos VMs de conjunto de escala. Eis um exemplo da CLI do Azure:
```
az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group Team-03 --vmss-name nt01 --settings "{'workspaceId': '<your workspace ID here>'}" --protected-settings "{'workspaceKey': '<your workspace key here'}"
```
Pode encontrar o necessário workspaceId e workspaceKey na área de trabalho do Log Analytics do portal do Azure. Na página Descrição geral, clique no mosaico de definições. Clique no separador de origens ligadas na parte superior.

> [!NOTE]
> Se a _atualização_ do conjunto de escala estiver definida para Manual, terá de aplicar a extensão a todos os VMs definidos, ligando para a atualização dos mesmos. No CLI, estas seriam _az vmss update-instances_.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="how-do-i-turn-on-boot-diagnostics"></a>Como ativar os diagnósticos de arranque?

Para ativar o diagnóstico de arranque, primeiro, crie uma conta de armazenamento. Em seguida, coloque este bloco JSON na sua escala virtual de escala de **máquinas virtualMachineProfile**, e atualize o conjunto de escala de máquina virtual:

```json
"diagnosticsProfile": {
    "bootDiagnostics": {
        "enabled": true,
        "storageUri": "http://yourstorageaccount.blob.core.windows.net"
    }
}
```

Quando é criada uma nova VM, a propriedade InstanceView da VM mostra os detalhes para a captura de ecrã e assim por diante. Segue-se um exemplo:

```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
}
```

## <a name="virtual-machine-properties"></a>Propriedades da máquina virtual

### <a name="how-do-i-get-property-information-for-each-vm-without-making-multiple-calls-for-example-how-would-i-get-the-fault-domain-for-each-of-the-100-vms-in-my-virtual-machine-scale-set"></a>Como posso obter informações sobre propriedades para cada VM sem fazer várias chamadas? Por exemplo, como seria posso obter o domínio de falhas para cada uma das 100 VMs no meu conjunto de dimensionamento de máquina virtual?

Para obter informações sobre propriedades para cada VM sem fazer várias chamadas, pode ligar para `ListVMInstanceViews` fazendo um `GET` REST API sobre o seguinte recurso URI:

/subscriptions/ < subscription_id > /resourceGroups/ < resource_group_name > /providers/Microsoft.Compute/virtualMachineScaleSets/ < scaleset_name > / virtualMachines? $expand = instanceView e $select = instanceView

### <a name="can-i-pass-different-extension-arguments-to-different-vms-in-a-virtual-machine-scale-set"></a>Pode passar argumentos de extensão diferentes para diferentes VMs num conjunto de dimensionamento de máquina virtual?

Não, não é possível passar os argumentos de extensão diferentes para diferentes VMs num conjunto de dimensionamento de máquina virtual. No entanto, as extensões podem agir com base nas propriedades da VM em que são executadas no, tais como o nome do computador exclusivas. As extensões também podem consultar metadados de instância sonâmlo em http://169.254.169.254 para obter mais informações sobre o VM.

### <a name="why-are-there-gaps-between-my-virtual-machine-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>Por que existem lacunas entre meu nomes de máquina VM de conjunto de dimensionamento de máquinas virtuais e os IDs de VM? Por exemplo: 0, 1, 3...

Existem lacunas entre os nomes das máquinas vM de escala virtual e os IDs VM porque a sua propriedade de **sobreoferta** de escala de máquina virtual está definida para o valor padrão de **verdadeiro**. Se o excesso de oferta for definido para **ser verdade,** são criados mais VMs do que solicitados. VMs adicionais, em seguida, são eliminadas. Neste caso, obter a implementação de uma maior fiabilidade, mas às custas de atribuição de nomes contíguo e contíguos tradução de endereços de rede (NAT) de regras.

Você pode definir esta propriedade como **falsa.** Para conjuntos de dimensionamento de máquina virtual pequena, isso não afeta significativamente a confiabilidade de implementação.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-virtual-machine-scale-set-and-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>O que é a diferença entre a eliminação de uma VM num conjunto de dimensionamento de máquina virtual e a desalocar a VM? Quando devo escolher cada uma delas?

A principal diferença entre eliminar um VM num conjunto de máquinavirtual e lidar com o VM é que `deallocate` não apaga os discos rígidos virtuais (VHDs). Existem custos de armazenamento associados à execução `stop deallocate`. Poderá utilizar um ou outro para um dos seguintes motivos:

- Pretende parar a pagar os custos de computação, mas pretende manter o estado do disco das VMs.
- Deseja iniciar um conjunto de VMs mais rapidamente do que pode aumentar horizontalmente a um conjunto de dimensionamento de máquina virtual.
  - Relacionadas com este cenário, pode ter criado seu próprio mecanismo de dimensionamento automático e pretender um dimensionamento de ponta a ponta mais rápido.
- Tem um conjunto de dimensionamento de máquinas virtuais que não pelo é distribuído por domínios de falha ou domínios de atualização. Isto poderá ser porque o tenha eliminado seletivamente VMs ou porque as VMs foram eliminadas depois de aprovisionar em excesso. O funcionamento `stop deallocate` seguido de `start` na escala de máquina virtual distribuída uniformemente os VMs através de domínios de falha ou domínios de atualização.

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
$snapshotName = "myShapshot"
$snapshot = Get-AzSnapshot -ResourceGroupName $rgname -SnapshotName $snapshotName  
$diskConfig = New-AzDiskConfig -AccountType Premium_LRS -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
$osDisk = New-AzDisk -Disk $diskConfig -ResourceGroupName $rgname -DiskName ($snapshotName + '_Disk')
```
