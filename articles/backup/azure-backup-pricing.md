---
title: Preços do Azure Backup
description: Saiba como calcular os seus custos para orçamentar os preços do Azure Backup.
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 03ec0076d3089562ddaace6db413fb3f1ba949a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88654536"
---
# <a name="azure-backup-pricing"></a>Preços do Azure Backup

Para saber mais sobre os preços do Azure Backup, visite a [página de preços do Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

## <a name="download-detailed-estimates-for-azure-backup-pricing"></a>Transferir estimativas detalhadas de preços do Azure Backup

Se pretende calcular os custos para fins de orçamentação ou comparação de custos, transfira o [simulador de preços do Azure Backup](https://aka.ms/AzureBackupCostEstimates) detalhado.  

### <a name="what-does-the-estimator-contain"></a>O que contém o simulador?

A folha do simulador de custos do Azure Backup tem uma opção para calcular todas as cargas de trabalho possíveis para as quais pretende fazer cópia de segurança com o Azure Backup. Estas cargas de trabalho incluem:

- VMs do Azure
- Servidores no local
- SQL nas VMs do Azure
- SAP HANA nas VMs do Azure
- Partilhas de ficheiros do Azure

## <a name="estimate-costs-for-backing-up-azure-vms-or-on-premises-servers"></a>Calcular os custos das cópias de segurança de VMs do Azure ou de servidores no local

Para calcular os custos da cópia de segurança de VMs do Azure ou de servidores no local com o Azure Backup, vai precisar dos seguintes parâmetros:

- Tamanho das VMs ou dos servidores no local dos quais está a tentar fazer cópia de segurança
  - Introduza o "tamanho utilizado" dos discos ou servidores necessários para a cópia de segurança

- Número de servidores com esse tamanho

- Qual é a quantidade de alterações a dados esperada nestes servidores?<br>
  Tal refere-se à quantidade de dados alterados. Por exemplo, se tiver uma VM com 200 GB de dados para fazer uma cópia de segurança e 10 GB desses dados são alterados todos os dias, a alteração diária é de 5%.

  - Uma alteração mais elevada significa que faz uma cópia de segurança de um maior número de dados

  - Escolha **Baixa** ou **Moderada** para servidores de ficheiros e **Alta** se estiver a executar bases de dados

  - Se conhece a sua **%dealteração**, pode utilizar a opção **Introduzir a sua própria %**

- Escolha a política de cópias de segurança

  - Por quanto tempo espera reter as cópias de segurança "Diárias"? (em dias)

  - Por quanto tempo espera reter as cópias de segurança "Semanais"? (em semanas)

  - Por quanto tempo espera reter as cópias de segurança "Mensais"? (em meses)

  - Por quanto tempo espera reter as cópias de segurança "Anuais"? (em anos)

  - Por quanto tempo espera reter os "Instantâneos de restauro instantâneo"? (1–5 dias)

    - Esta opção permite-lhe restaurar até sete dias de forma rápida com instantâneos armazenados em discos.

- **Opcional** – Cópia de segurança de Disco Seletiva

  - Se estiver a utilizar a opção **Cópia de Segurança de Disco Seletiva** durante a cópia de segurança de VMs do Azure, escolha a opção **Excluir Disco** e introduza a percentagem de discos excluídos da cópia de segurança em termos de tamanho. Por exemplo, se tiver uma VM ligada a três discos com 200 GB utilizados em cada disco e quiser excluir dois deles da cópia de segurança, introduza 66,7%.

- **Opcional** – Redundância de Armazenamento de Cópias de Segurança

  - Tal indica a redundância da Conta de Armazenamento na qual são guardados os dados da cópia de segurança. Recomendamos a utilização de **GRS** para obter a maior disponibilidade. Uma vez que garante que uma cópia dos seus dados da cópia de segurança é mantida numa região diferente, ajuda a cumprir vários padrões de conformidade. Altere a redundância para **LRS** se estiver a fazer cópia de segurança de ambientes de desenvolvimento ou teste que não precisam de uma cópia de segurança de nível empresarial. Selecione a opção **RAGRS** na folha se pretender compreender os custos quando o [Restauro Entre Regiões](backup-azure-arm-restore-vms.md#cross-region-restore) estiver ativado para as suas cópias de segurança.

- **Opcional** – Modificar preços regionais ou aplicar tarifas com desconto

  - Se quiser verificar as suas estimativas para uma região diferente ou tarifas com desconto, selecione **Sim** na opção **Experimentar estimativas para uma região diferente?** e introduza as tarifas com as quais pretende executar as estimativas.

## <a name="estimate-costs-for-backing-up-sql-servers-in-azure-vms"></a>Calcular os custos das cópias de segurança dos servidores SQL nas VMs do Azure

Para calcular os custos da cópia de segurança dos servidores SQL em execução nas VMs do Azure com o Azure Backup, vai precisar dos seguintes parâmetros:

- Tamanho dos servidores SQL dos quais está a tentar fazer cópia de segurança

- Número de servidores SQL com o tamanho acima

- Qual é a compressão esperada dos dados da cópia de segurança dos seus servidores SQL?

  - A maioria dos clientes do Azure Backup vê que os dados da cópia de segurança têm 80% de compressão em comparação com o tamanho do servidor SQL quando a compressão de SQL está **ativada**.

  - Se espera ver uma compressão diferente, introduza o número neste campo

- Qual é o tamanho esperado das cópias de segurança de registo?

  - A % indica o tamanho diário do registo como uma % do tamanho do servidor SQL

- Qual é a quantidade de alterações a dados diária esperada nestes servidores?

  - Normalmente, as bases de dados têm uma alteração "Alta"

  - Se conhece a sua **%dealteração**, pode utilizar a opção **Introduzir a sua própria %**

- Escolha a política de cópias de segurança

  - Tipo de Cópia de Segurança

    - A política mais eficaz que pode escolher é **Diferenciais diárias** com cópias de segurança completas semanais/mensais/anuais. O Azure Backup também pode restaurar a partir de diferenciais através de um clique.

    - Pode ainda optar por ter uma política com cópias de segurança completas diárias/semanais/mensais/anuais. Esta opção vai consumir um pouco mais de armazenamento do que a primeira opção.

  - Por quanto tempo espera reter as cópias de segurança de "registo"? (em dias) [7–35]

  - Por quanto tempo espera reter as cópias de segurança "Diárias"? (em dias)

  - Por quanto tempo espera reter as cópias de segurança "Semanais"? (em semanas)

  - Por quanto tempo espera reter as cópias de segurança "Mensais"? (em meses)

  - Por quanto tempo espera reter as cópias de segurança "Anuais"? (em anos)

- **Opcional** – Redundância de Armazenamento de Cópias de Segurança

  - Tal indica a redundância da Conta de Armazenamento na qual são guardados os dados da cópia de segurança. Recomendamos a utilização de **GRS** para obter a maior disponibilidade. Uma vez que garante que uma cópia dos seus dados da cópia de segurança é mantida numa região diferente, ajuda a cumprir vários padrões de conformidade. Altere a redundância para **LRS** se estiver a fazer cópia de segurança de ambientes de desenvolvimento ou teste que não precisam de uma cópia de segurança de nível empresarial.

- **Opcional** – Modificar preços regionais ou aplicar tarifas com desconto

  - Se quiser verificar as suas estimativas para uma região diferente ou tarifas com desconto, selecione **Sim** na opção **Experimentar estimativas para uma região diferente?** e introduza as tarifas com as quais pretende executar as estimativas.

## <a name="estimate-costs-for-backing-up-sap-hana-servers-in-azure-vms"></a>Calcular os custos das cópias de segurança dos servidores SAP HANA nas VMs do Azure

Para calcular os custos da cópia de segurança dos servidores SAP HANA em execução nas VMs do Azure com o Azure Backup, vai precisar dos seguintes parâmetros:

- Tamanho total das bases de dados SAP HANA das quais está a tentar fazer cópia de segurança. Tal deve ser a soma do tamanho da cópia de segurança completa de cada uma das bases de dados, conforme indicado pelo SAP HANA.
- Número de servidores SAP HANA com o tamanho acima
- Qual é o tamanho esperado das cópias de segurança de registo?
  
  - A % indica o tamanho médio diário do registo como uma % do tamanho total das bases de dados SAP HANA das quais está a fazer cópia de segurança no servidor SAP HANA
- Qual é a quantidade de alterações a dados diária esperada nestes servidores?
  - A % indica o tamanho médio diário da alteração como uma % do tamanho total das bases de dados SAP HANA das quais está a fazer cópia de segurança no servidor SAP HANA
  - Normalmente, as bases de dados têm uma alteração "Alta"
  - Se conhece a sua **%dealteração**, pode utilizar a opção **Introduzir a sua própria %**
- Escolha a política de cópias de segurança
  - Tipo de Cópia de Segurança
    - A política mais eficaz que pode escolher é **Diferenciais diárias** com cópias de segurança completas **semanais/mensais/anuais**. O Azure Backup também pode restaurar a partir de diferenciais através de um clique.
    - Pode ainda optar por ter uma política com cópias de segurança completas **diárias/semanais/mensais/anuais**. Esta opção vai consumir um pouco mais de armazenamento do que a primeira opção.
  - Por quanto tempo espera reter as cópias de segurança de "registo"? (em dias) [7–35]
  - Por quanto tempo espera reter as cópias de segurança "Diárias"? (em dias)
  - Por quanto tempo espera reter as cópias de segurança "Semanais"? (em semanas)
  - Por quanto tempo espera reter as cópias de segurança "Mensais"? (em meses)
  - Por quanto tempo espera reter as cópias de segurança "Anuais"? (em anos)
- **Opcional** – Redundância de Armazenamento de Cópias de Segurança
  
  - Tal indica a redundância da Conta de Armazenamento na qual são guardados os dados da cópia de segurança. Recomendamos a utilização de **GRS** para obter a maior disponibilidade. Uma vez que garante que uma cópia dos seus dados da cópia de segurança é mantida numa região diferente, ajuda a cumprir vários padrões de conformidade. Altere a redundância para **LRS** se estiver a fazer cópia de segurança de ambientes de desenvolvimento ou teste que não precisam de uma cópia de segurança de nível empresarial.
- **Opcional** – Modificar preços regionais ou aplicar tarifas com desconto
  
  - Se quiser verificar as suas estimativas para uma região diferente ou tarifas com desconto, selecione **Sim** na opção **Experimentar estimativas para uma região diferente?** e introduza as tarifas com as quais pretende executar as estimativas.
  
## <a name="estimate-costs-for-backing-up-azure-file-shares"></a>Calcular os custos das cópias de segurança das partilhas de ficheiros do Azure

Para calcular os custos da cópia de segurança das partilhas de ficheiros do Azure com a [solução de cópia de segurança baseada em instantâneos](azure-file-share-backup-overview.md) oferecida pelo Azure Backup, vai precisar dos seguintes parâmetros:

- Tamanho (**em GB**) das partilhas de ficheiros das quais pretende fazer cópia de segurança.

- Se pretende fazer cópia de segurança de partilhas de ficheiros distribuídas entre várias contas de armazenamento, especifique o número de contas de armazenamento que alojam as partilhas de ficheiros com o tamanho acima.

- Quantidade de alterações a dados esperada nas partilhas de ficheiros das quais pretende fazer cópia de segurança. <br>A alteração refere-se à quantidade de alterações nos dados e afeta diretamente o tamanho de armazenamento dos instantâneos. Por exemplo, se tiver uma partilha de ficheiros com 200 GB de dados para fazer uma cópia de segurança e 10 GB desses dados são alterados todos os dias, a alteração diária é de 5%.
  - Uma alteração mais elevada significa que a quantidade de alterações a dados nos conteúdos da partilha de ficheiros todos os dias é alta e, portanto, o tamanho do instantâneo incremental (que captura apenas as alterações nos dados) também seria maior.
  - Selecione Baixa (1%), Moderada (3%) ou Alta (5%) com base nas características e na utilização da partilha de ficheiros.
  - Se souber a **%dealteração** exata da sua partilha de ficheiros, pode selecionar a opção **Introduzir a sua própria %** na lista pendente. Especifique os valores (em %) para a alteração diária, semanal, mensal e anual.

- Tipo de conta de armazenamento (standard ou premium) e a definição de redundância de armazenamento da conta de armazenamento que aloja a partilha de ficheiros incluída na cópia de segurança. <br>Na solução de cópia de segurança atual para partilhas de ficheiros do Azure, os instantâneos são armazenados na mesma conta de armazenamento que a partilha de ficheiros incluída na cópia de segurança. Portanto, o custo de armazenamento associado aos instantâneos é cobrado como parte da sua fatura de ficheiros do Azure, com base nos preços dos instantâneos para o tipo de conta e a definição de redundância da conta de armazenamento que aloja a partilha de ficheiros e os instantâneos incluídos na cópia de segurança.

- Retenção de diferentes cópias de segurança
  - Por quanto tempo espera reter as cópias de segurança "Diárias"? (em dias)
  - Por quanto tempo espera reter as cópias de segurança "Semanais"? (em semanas)
  - Por quanto tempo espera reter as cópias de segurança "Mensais"? (em meses)
  - Por quanto tempo espera reter as cópias de segurança "Anuais"? (em anos)

  Veja a [matriz de suporte da partilha de ficheiros do Azure](azure-file-share-support-matrix.md#retention-limits) para obter os valores máximos de retenção suportados em cada categoria.

- **Opcional** – Modificar preços regionais ou aplicar tarifas com desconto.
  - Os valores predefinidos para o custo de armazenamento de instantâneos por GB e o custo da instância protegida no simulador referem-se à região E.U.A. Leste. Se quiser verificar as suas estimativas para uma região diferente ou tarifas com desconto, selecione **Sim** na opção **Experimentar estimativas para uma região diferente?** e introduza as tarifas com as quais pretende executar as estimativas.

## <a name="next-steps"></a>Passos seguintes

[O que é o serviço Azure Backup?](backup-overview.md)
