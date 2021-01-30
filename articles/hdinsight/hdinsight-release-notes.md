---
title: Notas de versão do Azure HDInsight
description: Últimas notas de lançamento para Azure HDInsight. Obtenha dicas de desenvolvimento e detalhes para Hadoop, Spark, R Server, Hive, e muito mais.
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 88e2161cfddf95f7f250b8b76c067d045f1529da
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/30/2021
ms.locfileid: "99092239"
---
# <a name="azure-hdinsight-release-notes"></a>Notas de lançamento do Azure HDInsight

Este artigo fornece informações sobre as **mais recentes** atualizações de lançamento do Azure HDInsight. Para obter informações sobre ver lançamentos anteriores, consulte [o HDInsight Release Notes Archive](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Resumo

O Azure HDInsight é um dos serviços mais populares entre os clientes empresariais para análises de código aberto em Azure.

Se quiser subscrever as notas de lançamento, assista a lançamentos [neste repositório GitHub](https://github.com/hdinsight/release-notes/releases).

## <a name="release-date-11182020"></a>Data de lançamento: 11/18/2020

Esta versão aplica-se tanto para HDInsight 3.6 como HDInsight 4.0. O lançamento hdInsight é disponibilizado a todas as regiões ao longo de vários dias. A data de lançamento aqui indica a primeira data de lançamento da região. Se não vir alterações abaixo, aguarde que o lançamento seja transmitido ao vivo na sua região dentro de vários dias.

## <a name="new-features"></a>Novas funcionalidades
### <a name="auto-key-rotation-for-customer-managed-key-encryption-at-rest"></a>Rotação da chave automática para encriptação da chave gerida pelo cliente em repouso
A partir desta versão, os clientes podem utilizar urLs de encriptação sem versão Azure KeyValut para encriptação de chaves gerida pelo cliente em repouso. O HDInsight roda automaticamente as teclas à medida que caducam ou são substituídas por novas versões. Saiba mais detalhes [aqui.](./disk-encryption.md)

### <a name="ability-to-select-different-zookeeper-virtual-machine-sizes-for-spark-hadoop-and-ml-services"></a>Capacidade de selecionar diferentes tamanhos de máquina virtual Zookeeper para serviços spark, Hadoop e ML
A HDInsight anteriormente não suportava a personalização do tamanho do nóleiro zookeeper para tipos de cluster Spark, Hadoop e ML Services. É predefinido para A2_v2/A2 tamanhos de máquina virtual, que são fornecidos gratuitamente. A partir desta versão, pode selecionar um tamanho de máquina virtual Zookeeper que seja mais apropriado para o seu cenário. Serão carregados os nós zookeeper com o tamanho da máquina virtual que não A2_v2/A2. A2_v2 e as máquinas virtuais A2 ainda são fornecidas gratuitamente.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Movendo-se para conjuntos de escala de máquina virtual Azure
O HDInsight agora utiliza máquinas virtuais Azure para abastecer o cluster. A partir desta versão, o serviço migrará gradualmente para [conjuntos de escala de máquina virtual Azure](../virtual-machine-scale-sets/overview.md). Todo o processo pode levar meses. Após a migração das suas regiões e subscrições, os clusters HDInsight recém-criados serão executados em conjuntos de escala de máquinas virtuais sem ações do cliente. Não se espera mudanças de rutura.

## <a name="deprecation"></a>Preterição
### <a name="deprecation-of-hdinsight-36-ml-services-cluster"></a>Depreciação do cluster hdInsight 3.6 ML Services
O tipo de cluster de serviços HDInsight 3.6 ML Services estará terminando o suporte até 31 de dezembro de 2020. Os clientes não poderão criar novos clusters de 3,6 ML Services a partir de 31 de dezembro de 2020. Os clusters existentes funcionarão como está sem o suporte da Microsoft. Consulte [aqui](./hdinsight-component-versioning.md#available-versions)a expiração do suporte para versões HDInsight e tipos de cluster .

### <a name="disabled-vm-sizes"></a>Tamanhos VM desativados
A partir de 16 de novembro de 2020, o HDInsight vai bloquear novos clientes que criam clusters utilizando standand_A8, standand_A9, standand_A10 e standand_A11 tamanhos VM. Os clientes existentes que utilizaram estes tamanhos VM nos últimos três meses não serão afetados. A partir de 9 de janeiro de 2021, o HDInsight bloqueará todos os clientes que criam clusters utilizando standand_A8, standand_A9, standand_A10 e standand_A11 tamanhos VM. Os aglomerados existentes funcionarão como estão. Considere mudar-se para HDInsight 4.0 para evitar uma possível interrupção do sistema/suporte.

## <a name="behavior-changes"></a>Mudanças de comportamento
### <a name="add-nsg-rule-checking-before-scaling-operation"></a>Adicione verificação de regras NSG antes da operação de escalonamento
HDInsight adicionou grupos de segurança de rede (NSGs) e rotas definidas pelo utilizador (UDRs) verificando com a operação de escala. A mesma validação é feita para a escala de clusters, além da criação de clusters. Esta validação ajuda a prevenir erros imprevisíveis. Se a validação não passar, o escalonamento falha. Saiba mais sobre como configurar os NSGs e uDRs corretamente, consulte [os endereços IP de gestão hdInsight](./hdinsight-management-ip-addresses.md).

## <a name="upcoming-changes"></a>Próximas alterações
As seguintes alterações irão ocorrer nos próximos lançamentos.

### <a name="breaking-change-for-net-for-apache-spark-100"></a>Quebra de mudança para .NET para Apache Spark 1.0.0
O HDInsight apresentará o primeiro grande lançamento oficial de .NET para Apache Spark no próximo lançamento. Fornece a completude da API dataFrame para Spark 2.4.x e Spark 3.0.x juntamente com outras funcionalidades. Haverá alterações para esta versão importante, consulte [este guia de migração](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) para entender as medidas necessárias para atualizar o seu código e os seus oleodutos. Saiba mais [aqui.](https://docs.microsoft.com/azure/hdinsight/spark/spark-dotnet-version-update#using-net-for-apache-spark-v10-in-hdinsight)

### <a name="default-cluster-vm-size-will-be-changed-to-ev3-family"></a>O tamanho VM do cluster padrão será alterado para família Ev3
A partir do próximo lançamento (por volta do final de janeiro), os tamanhos de VM de cluster padrão serão alterados de família D para família Ev3. Esta alteração aplica-se aos nós da cabeça e aos nós dos trabalhadores. Para evitar esta alteração, especifique os tamanhos VM que pretende utilizar no modelo ARM.

### <a name="default-cluster-version-will-be-changed-to-40"></a>A versão padrão do cluster será alterada para 4.0
A partir de fevereiro de 2021, a versão padrão do cluster HDInsight será alterada de 3.6 para 4.0. Para obter mais informações sobre as versões disponíveis, consulte as [versões disponíveis.](./hdinsight-component-versioning.md#available-versions) Saiba mais sobre as novidades em [HDInsight 4.0](./hdinsight-version-release.md)

### <a name="os-version-upgrade"></a>Upgrade da versão OS
O HDInsight está a atualizar a versão OS de 16.04 a 18.04. A atualização estará concluída antes de abril de 2021.

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>HDInsight 3.6 fim de suporte em 30 de junho de 2021
HDInsight 3.6 será o fim do suporte. A partir de 30 de junho de 2021, os clientes não podem criar novos clusters HDInsight 3.6. Os clusters existentes funcionarão como está sem o suporte da Microsoft. Considere mudar-se para HDInsight 4.0 para evitar uma possível interrupção do sistema/suporte.

## <a name="bug-fixes"></a>Correções de erros
O HDInsight continua a fazer melhorias na fiabilidade do cluster e no desempenho. 

## <a name="component-version-change"></a>Alteração da versão do componente
Nenhuma alteração da versão componente para esta versão. Pode encontrar as versões componentes atuais para HDInsight 4.0 e HDInsight 3.6 [neste doc](./hdinsight-component-versioning.md).

## <a name="known-issues"></a>Problemas conhecidos
### <a name="prevent-hdinsight-cluster-vms-from-rebooting-periodically"></a>Impedir que as VMs do cluster do HDInsight reiniciem periodicamente

A partir de meados de novembro de 2020, pode ter reparado que os VMs do cluster HDInsight são reiniciados regularmente. Isto pode ser causado por:

1.  O Clamav está ativado no seu aglomerado. O novo pacote azsec-clamav consome uma grande quantidade de memória que desencadeia o reinício do nó. 
2.  É programado um trabalho cron diariamente que monitoriza as alterações à lista de autoridades de certificados (AC) utilizadas pelos serviços Azure. Quando um novo certificado de CA está disponível, o script adiciona o certificado à loja de confiança JDK e agenda um reboot.

O HDInsight está a implementar correções e a aplicar patch para todos os clusters de execução para ambos os problemas. Para aplicar a correção imediatamente e evitar o reinício inesperado dos VMs, pode correr abaixo as ações de script em todos os nós do cluster como uma ação persistente do script. O HDInsight publicará outro aviso após a correção e remendação completa.
```
https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/replace_cacert_script.sh
https://healingscriptssa.blob.core.windows.net/healingscripts/ChangeOOMPolicyAndApplyLatestConfigForClamav.sh
```