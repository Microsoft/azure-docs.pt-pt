---
title: Preços do Azure Backup
description: Saiba como estimar os seus custos para orçamentar os preços de reserva da Azure.
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 03ec0076d3089562ddaace6db413fb3f1ba949a6
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88654536"
---
# <a name="azure-backup-pricing"></a>Preços do Azure Backup

Para saber mais sobre os preços de backup da Azure, visite a [página de preços de backup da Azure](https://azure.microsoft.com/pricing/details/backup/).

## <a name="download-detailed-estimates-for-azure-backup-pricing"></a>Faça o download de estimativas detalhadas para preços de backup Azure

Se procura estimar os seus custos para fins de orçamentação ou comparação de custos, baixe o estimador de preços detalhado da [Azure Backup.](https://aka.ms/AzureBackupCostEstimates)  

### <a name="what-does-the-estimator-contain"></a>O que o estimador contém?

A folha de estimativa de custos Azure Backup tem uma opção para você estimar todas as possíveis cargas de trabalho que você está procurando apoiar usando Azure Backup. Estas cargas de trabalho incluem:

- VMs do Azure
- Servidores no local
- SQL em VMs Azure
- SAP HANA em Azure VMs
- Azure arquiva ações

## <a name="estimate-costs-for-backing-up-azure-vms-or-on-premises-servers"></a>Estimativa de custos para o backup de VMs Azure ou servidores no local

Para estimar os custos de backup de VMs Azure ou servidores no local usando Azure Backup, você precisará dos seguintes parâmetros:

- Tamanho dos VMs ou servidores no local que está a tentar fazer recuar
  - Introduza o "tamanho usado" dos discos ou servidores necessários para serem apoiados

- Número de servidores com este tamanho

- Qual é a quantidade esperada de dados nestes servidores?<br>
  Churn refere-se à quantidade de alteração de dados. Por exemplo, se tivesse um VM com 200 GB de dados a ser apoiado e 10 GB do mesmo muda todos os dias, o churn diário é de 5%.

  - Maior agitação significará que você faz recuar mais dados

  - Escolha **Baixo** ou **Moderado** para servidores de ficheiros e **Alto** se estiver a executar bases de dados

  - Se conhece o **seu churn%**, pode usar a opção **Entrar a sua própria** parte

- Escolha a política de backup

  - Quanto tempo espera reter reforços "Daily"? (em dias)

  - Quanto tempo espera manter os reforços "Weekly"? (em semanas)

  - Quanto tempo espera reter reforços "Mensais"? (em meses)

  - Quanto tempo espera manter os reforços "Anualmente"? (em anos)

  - Quanto tempo espera reter "Instantâneo restaurar instantâneos"? (1-5 dias)

    - Esta opção permite restaurar desde há sete dias de forma rápida usando instantâneos armazenados em discos.

- **Opcional** - Backup de disco seletivo

  - Se estiver a utilizar a opção **de backup de disco seletivo** enquanto faz o backup dos VMs Azure, escolha a opção Disco **Excluído** e introduza a percentagem de discos excluídos da cópia de segurança em termos de tamanho. Por exemplo, se tiver um VM ligado a três discos com 200 GB utilizados em cada disco e se quiser excluir dois deles de fazer backup, introduza 66,7%.

- **Opcional** - Redundância de Armazenamento de Backup

  - Isto indica que o despedimento da Conta de Armazenamento os seus dados de backup entram. Recomendamos a utilização **de GRS** para obter a maior disponibilidade. Uma vez que garante que uma cópia dos seus dados de backup é mantida numa região diferente, ajuda-o a cumprir vários padrões de conformidade. Mude a redundância para **LRS** se estiver a apoiar ambientes de desenvolvimento ou de teste que não precisem de uma cópia de segurança ao nível da empresa. Selecione a opção **RAGRS** na folha se quiser entender os custos quando [o Cross-Region Restore](backup-azure-arm-restore-vms.md#cross-region-restore) estiver ativado para as suas cópias de segurança.

- **Opcional** – Modificar os preços regionais ou aplicar tarifas com desconto

  - Se quiser verificar as suas estimativas para uma região diferente ou tarifas com desconto, selecione **Sim** para as **estimativas de Tentativa para uma região diferente?**

## <a name="estimate-costs-for-backing-up-sql-servers-in-azure-vms"></a>Estimativa de custos para o backup dos servidores SQL em VMs Azure

Para estimar os custos de backup dos servidores SQL em execução em VMs Azure usando Azure Backup, você precisará dos seguintes parâmetros:

- Tamanho dos servidores SQL que está a tentar fazer recuar

- Número de servidores SQL com o tamanho acima

- Qual é a compressão esperada para os dados de backup dos seus servidores SQL?

  - A maioria dos clientes da Azure Backup vê que os dados de backup têm 80% de compressão em comparação com o tamanho do servidor SQL quando a compressão SQL está **ativada**.

  - Se espera ver uma compressão diferente, insira o número neste campo

- Qual é o tamanho esperado de backups de registos?

  - O % indica o tamanho do registo diário em % do tamanho do servidor SQL

- Qual é a quantidade esperada de dados diários nestes servidores?

  - Tipicamente, as bases de dados têm "alto" churn

  - Se conhece o **seu churn%**, pode usar a opção **Entrar a sua própria** parte

- Escolha a política de backup

  - Tipo de Cópia de Segurança

    - A política mais eficaz que pode escolher é **os diferenciais diários** com backups completos semanais/mensais/ano. O Azure Backup também pode restaurar a partir de diferenciais através de um único clique.

    - Também pode optar por ter uma política com backups completos diários/semanais/mensais/mensais. Esta opção consumirá um pouco mais de armazenamento do que a primeira opção.

  - Quanto tempo espera reter cópias de segurança de "log"? (em dias) [7-35]

  - Quanto tempo espera reter reforços "Daily"? (em dias)

  - Quanto tempo espera manter os reforços "Weekly"? (em semanas)

  - Quanto tempo espera reter reforços "Mensais"? (em meses)

  - Quanto tempo espera manter os reforços "Anualmente"? (em anos)

- **Opcional** - Redundância de Armazenamento de Backup

  - Isto indica que o despedimento da Conta de Armazenamento os seus dados de backup entram. Recomendamos a utilização **de GRS** para obter a maior disponibilidade. Uma vez que garante que uma cópia dos seus dados de backup é mantida numa região diferente, ajuda-o a cumprir vários padrões de conformidade. Mude a redundância para **LRS** se estiver a apoiar ambientes de desenvolvimento ou de teste que não precisem de uma cópia de segurança ao nível da empresa.

- **Opcional** – Modificar os preços regionais ou aplicar tarifas com desconto

  - Se quiser verificar as suas estimativas para uma região diferente ou tarifas com desconto, selecione **Sim** para as **estimativas de Tentativa para uma região diferente?**

## <a name="estimate-costs-for-backing-up-sap-hana-servers-in-azure-vms"></a>Estimativa de custos para o backup dos servidores SAP HANA em VMs Azure

Para estimar os custos de backup dos servidores SAP HANA em execução em VMs Azure usando Azure Backup, você precisará dos seguintes parâmetros:

- Tamanho total das bases de dados SAP HANA que está a tentar fazer. Esta deve ser a soma do tamanho total da cópia de segurança de cada uma das bases de dados, conforme reportado pela SAP HANA.
- Número de servidores SAP HANA com o tamanho acima
- Qual é o tamanho esperado de backups de registos?
  
  - O % indica o tamanho médio do registo diário em % do tamanho total das bases de dados SAP HANA que está a fazer no servidor SAP HANA
- Qual é a quantidade esperada de dados diários nestes servidores?
  - O % indica o tamanho médio diário do churn em % do tamanho total das bases de dados SAP HANA que está a fazer no servidor SAP HANA
  - Tipicamente, as bases de dados têm "alto" churn
  - Se conhece o **seu churn%**, pode usar a opção **Entrar a sua própria** parte
- Escolha a política de backup
  - Tipo de Cópia de Segurança
    - A política mais eficaz que pode escolher é **os diferenciais diários** com backups **completos semanais/mensais/ano.** O Azure Backup também pode restaurar a partir de diferenciais através de um único clique.
    - Também pode optar por ter uma política com backups **completos diários/semanais/mensais/mensais.** Esta opção consumirá um pouco mais de armazenamento do que a primeira opção.
  - Quanto tempo espera reter cópias de segurança de "log"? (em dias) [7-35]
  - Quanto tempo espera reter reforços "Daily"? (em dias)
  - Quanto tempo espera manter os reforços "Weekly"? (em semanas)
  - Quanto tempo espera reter reforços "Mensais"? (em meses)
  - Quanto tempo espera manter os reforços "Anualmente"? (em anos)
- **Opcional** - Redundância de Armazenamento de Backup
  
  - Isto indica que o despedimento da Conta de Armazenamento os seus dados de backup entram. Recomendamos a utilização **de GRS** para obter a maior disponibilidade. Uma vez que garante que uma cópia dos seus dados de backup é mantida numa região diferente, ajuda-o a cumprir vários padrões de conformidade. Mude a redundância para **LRS** se estiver a apoiar ambientes de desenvolvimento ou de teste que não precisem de uma cópia de segurança ao nível da empresa.
- **Opcional** – Modificar os preços regionais ou aplicar tarifas com desconto
  
  - Se quiser verificar as suas estimativas para uma região diferente ou tarifas com desconto, selecione **Sim** para as **estimativas de Tentativa para uma região diferente?**
  
## <a name="estimate-costs-for-backing-up-azure-file-shares"></a>Estimativa de custos para o backup das ações de ficheiros Azure

Para estimar os custos de backup das ações de ficheiros Azure utilizando a [solução de backup baseada em instantâneos](azure-file-share-backup-overview.md) oferecida pela Azure Backup, você precisará dos seguintes parâmetros:

- Tamanho (**em GB**) das ações de ficheiro que pretende fazer recuar.

- Se pretender fazer o back up de ações de ficheiros espalhadas por várias contas de armazenamento, especifique o número de contas de armazenamento que hospedam as ações de ficheiro com o tamanho acima.

- A quantidade esperada de dados nas ações de ficheiro que pretende fazer back-up. <br>Churn refere-se à quantidade de alteração de dados e impacta diretamente o tamanho do armazenamento instantâneo. Por exemplo, se tiver uma partilha de ficheiros com 200 GB de dados a serem apoiados, e 10 GB dela mudar todos os dias, o churn diário é de 5%.
  - Maior agitação significa que a quantidade de alteração de dados no conteúdo da partilha de ficheiros todos os dias é alta, pelo que o tamanho do instantâneo incremental (capturando apenas as alterações de dados) também seria mais.
  - Selecione Baixo (1%), Moderado (3%), ou Alto (5%) com base nas características e utilização da sua partilha de ficheiros.
  - Se **souber** o preço exato da sua parte do ficheiro, pode selecionar a **opção Enter your own%** a partir da entrega. Especificar os valores (in %) para o dia, semanal, mensal e anual.

- Tipo de conta de armazenamento (standard ou premium) e a definição de redundância de armazenamento da conta de armazenamento que hospeda a parte do ficheiro back-up. <br>Na atual solução de backup para ações de ficheiros Azure, as imagens são armazenadas na mesma conta de armazenamento que a parte de ficheiros com cópia de segurança. Assim, o custo de armazenamento associado a instantâneos é faturado como parte da sua conta de ficheiros Azure, com base no preço instantâneo para o tipo de conta e definição de redundância da conta de armazenamento que hospeda a partilha de ficheiros e instantâneos de ficheiros com apoio.

- Retenção para diferentes backups
  - Quanto tempo espera reter reforços "Daily"? (em dias)
  - Quanto tempo espera manter os reforços "Weekly"? (em semanas)
  - Quanto tempo espera reter reforços "Mensais"? (em meses)
  - Quanto tempo espera manter os reforços "Anualmente"? (em anos)

  Consulte [a matriz de suporte de partilha do Ficheiro Azure](azure-file-share-support-matrix.md#retention-limits) para obter os valores máximos de retenção suportados em cada categoria.

- **Opcional** – Modificar os preços regionais ou aplicar tarifas com desconto.
  - Os valores predefinidos definidos para o custo de armazenamento instantâneo por GB e o custo de instância protegido no estimador são para a região leste dos EUA. Se quiser verificar as suas estimativas para uma região diferente ou tarifas com desconto, selecione **Sim** para as **estimativas de Tentativa para uma região diferente?**

## <a name="next-steps"></a>Passos seguintes

[O que é o serviço Azure Backup?](backup-overview.md)
