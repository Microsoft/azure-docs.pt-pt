---
title: Atualizar um cluster para usar o nome comum do certificado
description: Saiba como converter um certificado de cluster de tecido de serviço de declarações baseadas em impressões digitais para nomes comuns.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 224798565921593d3c91dfcc187efa71a71b1fdd
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368065"
---
# <a name="convert-cluster-certificates-from-thumbprint-based-declarations-to-common-names"></a>Converter certificados de agrupamento de declarações baseadas em impressão digital para nomes comuns
A assinatura de um certificado (coloquialmente conhecido como 'impressão digital') é única, o que significa que um certificado de cluster declarado por impressão digital refere-se a uma instância específica de um certificado. Isto, por sua vez, torna a caderga de certificados - e a gestão, em geral - difícil e explícita: cada alteração requer a orquestração de atualizações do cluster e dos anfitriões de computação subjacentes. A conversão das declarações de um agrupamento de tecidos de serviço, desde a impressão digital em polegares, a declarações baseadas no nome comum do certificado, simplifica consideravelmente a gestão - nomeadamente, a reversão de um certificado já não requer uma atualização do cluster. Este artigo descreve como converter um cluster existente em declarações comuns baseadas em nomes sem tempo de inatividade.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="moving-to-certificate-authority-ca-signed-certificates"></a>Passando para certificados assinados pela Autoridade de Certificados (CA)
A segurança de um cluster cujo certificado é declarado por impressão digital baseia-se no facto de ser impossível, ou computacionalmente inviável, forjar um certificado com a mesma assinatura que outro. Neste caso, a proveniência do certificado é menos importante, pelo que os certificados auto-assinados são adequados. Em contrapartida, a segurança de um cluster com certificados declarados com nome comum provém do serviço de Infraestruturas de Chaves Públicas (PKI) que emitiu esse certificado, e inclui aspetos como as suas práticas de certificação, quer a sua segurança operacional seja auditada e muitos outros. Por esta razão, a escolha de um PKI é importante, é necessário um conhecimento íntimo dos emitentes (Certificate Authority, ou CA) e os certificados auto-assinados são essencialmente inúteis. Um certificado declarado pelo nome comum (CN) é tipicamente considerado válido se a sua cadeia puder ser construída com sucesso, o sujeito tem o elemento CN esperado, e o seu emitente (imediato ou superior na cadeia) é confiável pelo agente que realiza a validação. A Service Fabric suporta a declaração de certificados pela CN com emitente "implícito" (a corrente deve terminar numa âncora fiduciu das fidedignidades), ou com emitentes declarados por impressão digital ("pinning emitente"); por favor, consulte este  [artigo](cluster-security-certificates.md#common-name-based-certificate-validation-declarations) para mais detalhes. Para converter um cluster utilizando um certificado auto-assinado declarado por impressão digital em comum, o certificado assinado pelo objetivo, o certificado assinado pela AC, deve ser introduzido pela primeira vez no cluster por impressão digital; só então é possível a conversão de TP para CN.

Para efeitos de teste, um certificado auto-assinado pode ser declarado pela CN, fixando o emitente à sua própria impressão digital; do ponto de vista da segurança, isto é quase equivalente a declarar o mesmo certificado pela TP. Note-se, no entanto, que uma conversão bem sucedida deste tipo não garante uma conversão bem sucedida de TP para CN com um certificado assinado pela CA. Por isso, recomenda-se testar a conversão com um certificado adequado, assinado pela AC (existem opções gratuitas).

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Carre faça o upload do certificado e instale-o no conjunto de escala
Em Azure, o mecanismo recomendado para a obtenção e fornecimento de certificados envolve o serviço Azure Key Vault e a sua ferramenta. Deve ser dotado um certificado correspondente à declaração do certificado de cluster a todos os nós dos conjuntos de escala de máquinas virtuais que compõem o seu cluster; consulte [segredos em conjuntos de escala de máquina virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-securely-ship-a-certificate-to-the-vm) para mais detalhes. É importante que os certificados de agrupamento de corrente e de destino sejam instalados nos VM de cada tipo de nó do cluster antes de escoar alterações nas declarações de certificados do cluster. A viagem da emissão de certificados ao provisionamento num nó de tecido de serviço é discutida em profundidade [aqui.](cluster-security-certificate-management.md#the-journey-of-a-certificate)

## <a name="bring-cluster-to-an-optimal-starting-state"></a>Traga cluster para um ótimo estado de partida
A conversão de uma declaração de certificado de impactos baseados em impressão digital para nome comum:

- Como cada nó no cluster encontra e apresenta as suas credenciais a outros nós
- Como cada nó valida as credenciais da sua contraparte ao estabelecer uma ligação segura  

Reveja as regras de [apresentação e validação para ambas as configurações](cluster-security-certificates.md#certificate-configuration-rules) antes de prosseguir. A consideração mais importante ao efetuar uma conversão de nome em polegar para o nome comum é que os nós atualizados e ainda não atualizados (isto é, nós pertencentes a diferentes domínios de atualização) devem ser capazes de realizar uma autenticação mútua bem sucedida a qualquer momento durante a atualização. A forma recomendada de o conseguir é declarar o certificado de alvo/objetivo por impressão digital numa atualização inicial e completar a transição para nome comum numa atualização posterior. Se o cluster já estiver num estado de partida recomendado, esta secção pode ser ignorada.

Existem vários estados de partida válidos para uma conversão; o invariante é que o cluster já está a utilizar o certificado-alvo (declarado por impressão digital) no início da atualização para nome comum. `GoalCert` `OldCert1` `OldCert2` Consideramos,

#### <a name="valid-starting-states"></a>Estados-ini por partida válidos
- `Thumbprint: GoalCert, ThumbprintSecondary: None`
- `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, onde `GoalCert` tem uma data posterior à `NotAfter` da `OldCert1`
- `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, onde `GoalCert` tem uma data posterior à `NotAfter` da `OldCert1`

Se o seu cluster não estiver num dos estados válidos acima descritos, consulte o apêndice para alcançar esse estado no final deste artigo.

## <a name="select-the-desired-common-name-based-certificate-validation-scheme"></a>Selecione o desejado sistema de validação de certificados com base em nomes comuns
Como descrito anteriormente, o Service Fabric suporta a declaração de certificados pela CN com uma âncora implícita de confiança, ou com a fixação explícita das impressões digitais do emitente. Por favor, consulte [este artigo](cluster-security-certificates.md#common-name-based-certificate-validation-declarations) para mais detalhes, e certifique-se de que tem uma boa compreensão das diferenças, e as implicações de escolher qualquer um dos mecanismos. Syntacticamente, esta diferença/escolha é determinada pelo valor do `certificateIssuerThumbprintList` parâmetro: vazio significa confiar numa raiz fidedigna CA (âncora de confiança), enquanto um conjunto de impressões digitais restringe os emitentes diretos permitidos dos certificados de agrupamento.

   > [!NOTE]
   > O campo 'certificateIssuerThumbprint' permite especificar os emitentes diretos esperados de certificados declarados pelo nome comum do sujeito. Os valores aceitáveis são uma ou mais impressões digitais SHA1 separadas por vírgula. Note que se trata de um reforço da validação do certificado - se nenhum emitente estiver especificado/lista estiver vazio, o certificado será aceite para autenticação se a sua cadeia puder ser construída, e acaba numa raiz fidedigna pelo validador. Se forem especificadas impressões digitais de um ou mais emissores, o certificado será aceite se a impressão digital do seu emitente direto, tal como extraído da cadeia, corresponder a qualquer um dos valores especificados neste campo - independentemente de a raiz ser ou não fidedigna. Note que um PKI pode utilizar diferentes autoridades de certificação ('emitentes') para assinar certificados com um determinado assunto, pelo que é importante especificar todas as impressões digitais emitentes esperadas para esse assunto. Por outras palavras, a renovação de um certificado não está garantida para ser assinada pelo mesmo emitente que o certificado que o certificado foi renovado.
   >
   > Especificar o emitente é considerado uma boa prática; ao omiti-lo continuará a funcionar - para certificados acorrentados até uma raiz de confiança - este comportamento tem limitações e pode ser eliminado gradualmente num futuro próximo. Note-se também que os clusters implantados em Azure, e protegidos com certificados X509 emitidos por um PKI privado e declarados por sujeitos podem não ser validados pelo serviço Azure Service Fabric (para comunicação cluster-to-service), se a Política de Certificados do PKI não for detetável, disponível e acessível. 

## <a name="update-the-clusters-azure-resource-management-arm-template-and-deploy"></a>Atualizar o modelo de gestão de recursos azure (ARM) do cluster e implementar
Recomenda-se gerir os clusters de tecido de serviço Azure com modelos ARM; uma alternativa, também utilizando artefactos json, é o [Azure Resource Explorer (pré-visualização)](https://resources.azure.com). Uma experiência equivalente não está disponível no portal Azure neste momento. Se o modelo original correspondente a um cluster existente não estiver disponível, um modelo equivalente pode ser obtido no portal Azure navegando para o grupo de recursos que contém o cluster, selecionando o modelo de **exportação** do menu esquerdo da **Automatização** e, em seguida, selecionando os recursos desejados; no mínimo, o conjunto da balança de máquinas virtuais e os recursos de cluster, respectivamente, devem ser exportados. O modelo gerado também pode ser descarregado. Note que este modelo pode exigir alterações antes de ser totalmente implantável, e pode não corresponder exatamente ao original - é um reflexo do estado atual do recurso cluster.

As alterações necessárias são as seguintes:
    - atualização da definição da extensão do nó de nó de tecido de serviço (sob o recurso de máquina virtual); se o cluster definir vários tipos de nós, terá de atualizar a definição de cada conjunto de escala de máquina virtual correspondente
    - atualizar a definição de recursos de cluster

Exemplos detalhados estão incluídos abaixo.

### <a name="updating-the-virtual-machine-scale-set-resources"></a>Atualizar os recursos de conjunto de escala de máquina virtual
De
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
Para
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

### <a name="updating-the-cluster-resource"></a>Atualizar o recurso cluster
No recurso **Microsoft.ServiceFabric/clusters,** adicione um **certificadoCommonNames** propriedade com uma definição **comum de Nomes** e remova completamente a propriedade do **certificado** (todas as suas definições):

De
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
Para
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

Para obter mais informações, consulte [implementar um cluster de tecido de serviço que utiliza o nome comum do certificado em vez de impressão digital.](./service-fabric-create-cluster-using-cert-cn.md)

## <a name="deploy-the-updated-template"></a>Implementar o modelo atualizado
Reimplante o modelo atualizado após efetuar as alterações.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="appendix-achieve-a-valid-starting-state-for-converting-a-cluster-to-cn-based-certificate-declarations"></a>Apêndice: obter um estado de partida válido para a conversão de um cluster em declarações de certificados baseados em CN

| Estado de partida | Upgrade 1 | Upgrade 2 |
| :--- | :--- | :--- |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` e `GoalCert` tem uma data posterior do `NotAfter` que `OldCert1` | `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` e `OldCert1` tem uma data posterior do `NotAfter` que `GoalCert` | `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1` | `Thumbprint: GoalCert, ThumbprintSecondary: None` |
| `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, onde `OldCert1` tem uma data posterior `NotAfter` que `GoalCert` | Upgrade para `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, onde `OldCert1` tem uma data posterior `NotAfter` que `GoalCert` | Upgrade para `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: OldCert2` | Remover um de `OldCert1` ou para chegar ao `OldCert2` estado `Thumbprint: OldCertx, ThumbprintSecondary: None` | Continue a partir do novo estado inicial |

Para obter instruções sobre como realizar qualquer uma destas atualizações, consulte [este documento](service-fabric-cluster-security-update-certs-azure.md).


## <a name="next-steps"></a>Passos seguintes
* Conheça a [segurança do cluster.](service-fabric-cluster-security.md)
* Saiba como [capotar um certificado de cluster com nome comum](service-fabric-cluster-rollover-cert-cn.md)
* Saiba [como configurar um cluster para autorollover sem toque](cluster-security-certificate-management.md)

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
