---
title: Atualizar um cluster para usar o nome comum do certificado
description: Saiba como converter um certificado de cluster de tecido de serviço Azure de declarações baseadas em impressões digitais para nomes comuns.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: daf8d55e156f30b1f9e9ec5c50d60714e6f17884
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308018"
---
# <a name="convert-cluster-certificates-from-thumbprint-based-declarations-to-common-names"></a>Converter certificados de agrupamento de declarações baseadas em impressão digital para nomes comuns

A assinatura de um certificado (vulgarmente conhecido como impressão digital) é única. Um certificado de agrupamento declarado por impressão digital refere-se a uma instância específica de um certificado. Esta especificidade torna a cadeência dos certificados e a gestão em geral, difícil e explícita. Cada mudança requer a orquestração de upgrades do cluster e dos anfitriões de computação subjacentes.

A conversão das declarações de certificados de um cluster de tecidos de serviço Azure, baseadas em impressões digitais em declarações baseadas no nome comum do certificado (CN) simplifica consideravelmente a gestão. Em particular, a reversão de um certificado já não requer uma atualização do cluster. Este artigo descreve como converter um cluster existente em declarações baseadas em CN sem tempo de inatividade.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-to-certificate-authority-signed-certificates"></a>Passar para certificados assinados pela autoridade

A segurança de um cluster cujo certificado é declarado por impressão digital baseia-se no facto de ser impossível, ou computacionalmente inviável, forjar um certificado com a mesma assinatura que outro. Neste caso, a proveniência do certificado é menos importante, pelo que os certificados auto-assinados são adequados.

Em contrapartida, a segurança de um cluster cujos certificados são declarados pela CN provém da confiança implícita que o proprietário do cluster tem no seu fornecedor de certificados. O prestador é o serviço de infraestrutura chave pública (PKI) que emitiu o certificado. A confiança baseia-se, entre outros fatores, nas práticas de certificação do PKI, quer a sua segurança operacional seja auditada e aprovada por outras partes de confiança, e assim por diante.

O proprietário do agrupamento deve também ter conhecimento pormenorizado dos quais as autoridades de certificados (AA) que emitem os seus certificados, uma vez que se trata de um aspeto fundamental da validação dos certificados por pessoa. Isto também implica que os certificados auto-assinados são totalmente inadequados para este fim. Literalmente, qualquer um pode gerar um certificado com um determinado assunto.

Um certificado declarado pela CN é normalmente considerado válido se:

* A sua cadeia pode ser construída com sucesso.
* O sujeito tem o elemento CN esperado.
* O seu emitente (imediato ou superior na cadeia) é confiável pelo agente que realiza a validação.

A Service Fabric suporta a declaração de certificados pela CN de duas formas:

* Com emitentes *implícitos,* o que significa que a corrente deve terminar numa âncora de confiança.
* Com emitentes declarados por impressão digital, que é conhecido como pinning emitente.

Para obter mais informações, consulte [as declarações de validação de certificados com base em nome comum](cluster-security-certificates.md#common-name-based-certificate-validation-declarations).

Para converter um cluster utilizando um certificado auto-assinado declarado por impressão digital para CN, o certificado assinado pelo alvo, o certificado assinado pela AC deve ser introduzido primeiro no cluster por impressão digital. Só então é possível a conversão da impressão digital para a CN.

Para efeitos de teste, um certificado auto-assinado *pode* ser declarado pela CN, mas apenas se o emitente estiver fixado à sua própria impressão digital. Do ponto de vista da segurança, esta ação é quase equivalente a declarar o mesmo certificado por impressão digital. Uma conversão bem sucedida deste tipo não garante uma conversão bem sucedida da impressão digital para a CN com um certificado assinado pela AC. Recomendamos que teste a conversão com um certificado adequado, assinado pela AC. Existem opções gratuitas para este teste.

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Carre faça o upload do certificado e instale-o no conjunto de escala

Em Azure, o mecanismo recomendado para a obtenção e fornecimento de certificados envolve o Azure Key Vault e a sua ferramenta. Deve ser fornecida uma declaração de certificado de agrupamento a todos os nós dos conjuntos de escala de máquina virtual que compõem o seu cluster. Para obter mais informações, consulte [Secrets em conjuntos de escala de máquina virtual.](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.yml#how-do-i-securely-ship-a-certificate-to-the-vm-)

É importante instalar certificados de cluster de corrente e alvo nas máquinas virtuais de cada tipo de nó do cluster antes de fazer alterações nas declarações de certificado do cluster. A viagem da emissão de certificados ao provisionamento num nó de tecido de serviço é discutida em profundidade na [viagem de um certificado](cluster-security-certificate-management.md#the-journey-of-a-certificate).

## <a name="bring-the-cluster-to-an-optimal-starting-state"></a>Traga o cluster para um ótimo estado de partida

Conversão de uma declaração de certificado de impactos baseados em impressão digital para cn:

- Como cada nó no aglomerado encontra e apresenta as suas credenciais a outros nós.
- Como cada nó valida as credenciais da sua contraparte ao estabelecer uma ligação segura.

Reveja as regras de [apresentação e validação para ambas as configurações](cluster-security-certificates.md#certificate-configuration-rules) antes de proceder. A consideração mais importante quando executa uma conversão thumbprint-to-CN é que os nós atualizados e ainda não atualizados (isto é, nós pertencentes a diferentes domínios de atualização) devem ser capazes de realizar uma autenticação mútua bem sucedida a qualquer momento durante a atualização. A forma recomendada de alcançar este comportamento é declarar o certificado de alvo ou objetivo por impressão digital numa atualização inicial. Em seguida, concluir a transição para a CN numa seguinte. Se o cluster já estiver num estado de partida recomendado, pode saltar esta secção.

Existem vários estados de partida válidos para uma conversão. O invariante é que o cluster já está a utilizar o certificado-alvo (declarado por impressão digital) no início da atualização para CN. Consideramos `GoalCert` , e neste `OldCert1` `OldCert2` artigo.

#### <a name="valid-starting-states"></a>Estados de partida válidos

- `Thumbprint: GoalCert, ThumbprintSecondary: None`
- `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, onde `GoalCert` tem uma data posterior à `NotBefore` da `OldCert1`
- `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, onde `GoalCert` tem uma data posterior à `NotBefore` da `OldCert1`

> [!NOTE]
> Antes da versão 7.2.445 (7.2 CU4), o Service Fabric selecionou o certificado de validade mais distante (o certificado com a propriedade 'NotAfter' mais distante), pelo que os estados de partida acima anteriores a 7.2 CU4 exigem que o GoalCert tenha uma `NotAfter` data posterior ao do que `OldCert1`

Se o seu cluster não estiver num dos estados válidos previamente descritos, consulte informações sobre a obtenção desse estado na secção no final deste artigo.

## <a name="select-the-desired-cn-based-certificate-validation-scheme"></a>Selecione o desejado sistema de validação de certificados baseado em CN

Como descrito anteriormente, o Service Fabric suporta a declaração de certificados pela CN com uma âncora implícita de confiança ou com a fixação explícita das impressões digitais do emitente. Para obter mais informações, consulte [as declarações de validação de certificados com base em nome comum](cluster-security-certificates.md#common-name-based-certificate-validation-declarations).

Certifique-se de que tem uma boa compreensão das diferenças e das implicações da escolha de qualquer um dos mecanismos. Sinticamente, esta diferença ou escolha é determinada pelo valor do `certificateIssuerThumbprintList` parâmetro. Vazio significa confiar numa CA de raiz fidedigna (âncora de confiança), enquanto um conjunto de impressões digitais restringe os emitentes diretos permitidos de certificados de cluster.

   > [!NOTE]
   > O `certificateIssuerThumbprint` campo permite especificar os emitentes diretos esperados de certificados declarados pelo sujeito CN. Os valores aceitáveis são uma ou mais impressões digitais SHA1 separadas por vírgula. Esta ação reforça a validação do certificado.
   >
   > Se não forem especificados emitentes ou a lista estiver vazia, o certificado será aceite para autenticação se a sua cadeia puder ser construída. O certificado acaba então numa raiz fidedigna pelo validador. Se forem especificadas impressões digitais de um ou mais emissores, o certificado será aceite se a impressão digital do seu emitente direto, tal como extraído da cadeia, corresponder a qualquer um dos valores especificados neste campo. O certificado será aceite se a raiz é ou não confiável.
   >
   > Um PKI pode utilizar diferentes autoridades de certificação (também conhecidos como *emitentes)* para assinar certificados com um determinado assunto. Por esta razão, é importante especificar todas as impressões digitais esperadas para o assunto. Por outras palavras, a renovação de um certificado não é garantida para ser assinada pelo mesmo emitente que o certificado ser renovado.
   >
   > Especificar o emitente é considerado uma boa prática. Omitir o emitente continuará a trabalhar para certificados acorrentados até uma raiz de confiança, mas este comportamento tem limitações e pode ser eliminado num futuro próximo. Os clusters implantados em Azure, protegidos com certificados X509 emitidos por um PKI privado, e declarados por sujeito podem não ser validados pela Service Fabric (para comunicação cluster-to-service). A validação requer que a política de certificados do PKI seja detetável, disponível e acessível.

## <a name="update-the-clusters-azure-resource-manager-template-and-deploy"></a>Atualize o modelo de Gestor de Recursos Azure do cluster e implemente

Gerencie os seus clusters de tecido de serviço com modelos Azure Resource Manager (ARM). Uma alternativa, que também utiliza artefactos JSON, é o [Azure Resource Explorer (pré-visualização)](https://resources.azure.com). Uma experiência equivalente não está disponível no portal Azure neste momento.

Se o modelo original correspondente a um cluster existente não estiver disponível, um modelo equivalente pode ser obtido no portal Azure. Vá ao grupo de recursos que contém o cluster e selecione o modelo de **exportação** do menu **Automação** à esquerda. Em seguida, selecione os recursos que deseja. No mínimo, o conjunto de escalas de máquina virtual e os recursos de cluster, respectivamente, devem ser exportados. O modelo gerado também pode ser descarregado. Este modelo pode exigir alterações antes de ser totalmente implantável. O modelo também pode não corresponder ao original exatamente. É um reflexo do estado atual do recurso de cluster.

As alterações necessárias são as seguintes:

- Atualização da definição da extensão do nó de nó de tecido de serviço (sob o recurso da máquina virtual). Se o cluster definir vários tipos de nós, terá de atualizar a definição de cada conjunto de escalas de máquinas virtuais correspondentes.
- Atualização da definição de recursos de cluster.

Exemplos detalhados estão incluídos aqui.

### <a name="update-the-virtual-machine-scale-set-resources"></a>Atualizar os recursos de conjunto de escala de máquina virtual
De:
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "thumbprint": "[parameters('certificateThumbprint')]",
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```
Para:
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "commonNames": [
                                    "[parameters('certificateCommonName')]"
                                ],
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```

### <a name="update-the-cluster-resource"></a>Atualizar o recurso cluster

No recurso **Microsoft.ServiceFabric/clusters,** adicione uma propriedade **certificaCommonNames** com uma definição **comum de Nomes** e remova completamente a propriedade do **certificado** (todas as suas definições).

De:
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```
Para:
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificateCommonNames": {
                "commonNames": [
                    {
                        "certificateCommonName": "[parameters('certificateCommonName')]",
                        "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprintList')]"
                    }
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```

Para obter mais informações, consulte [implementar um cluster de tecido de serviço que utiliza o nome comum do certificado em vez da impressão digital](./service-fabric-create-cluster-using-cert-cn.md).

## <a name="deploy-the-updated-template"></a>Implementar o modelo atualizado

Reimplante o modelo atualizado depois de efetuar as alterações.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="achieve-a-valid-starting-state-for-converting-a-cluster-to-cn-based-certificate-declarations"></a>Obter um estado de partida válido para a conversão de um cluster em declarações de certificados baseados em CN

| Estado de partida | Upgrade 1 | Upgrade 2 |
| :--- | :--- | :--- |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` e `GoalCert` tem uma data posterior do `NotBefore` que `OldCert1` | `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` e `OldCert1` tem uma data posterior do `NotBefore` que `GoalCert` | `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1` | `Thumbprint: GoalCert, ThumbprintSecondary: None` |
| `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, onde `OldCert1` tem uma data posterior `NotBefore` que `GoalCert` | Upgrade para `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, onde `OldCert1` tem uma data posterior `NotBefore` que `GoalCert` | Upgrade para `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: OldCert2` | Remover um de `OldCert1` ou para chegar ao `OldCert2` estado `Thumbprint: OldCertx, ThumbprintSecondary: None` | Continue a partir do novo estado inicial |

> [!NOTE]
> Para um cluster numa versão anterior à versão 7.2.445 (7.2 CU4), `NotBefore` `NotAfter` substitua-o nos estados acima referidos.

Para obter instruções sobre como realizar qualquer uma destas atualizações, consulte [Gerir os certificados num cluster de tecidos de serviço Azure](service-fabric-cluster-security-update-certs-azure.md).

## <a name="next-steps"></a>Passos seguintes

* Conheça a [segurança do cluster.](service-fabric-cluster-security.md)
* Saiba como [passar um certificado de cluster com um nome comum](service-fabric-cluster-rollover-cert-cn.md).
* Aprenda a [configurar um cluster para autorollover sem toque](cluster-security-certificate-management.md).

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
