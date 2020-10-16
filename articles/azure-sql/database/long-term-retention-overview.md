---
title: Retenção de cópia de segurança de longa duração
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Saiba como a Azure SQL Database & Azure SQL Managed Instance suporta o armazenamento de backups completos da base de dados por até 10 anos através da política de retenção a longo prazo.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 05/18/2019
ms.openlocfilehash: 83d3bb78ef27af377b0a8c5edf75f658a0ca93e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91450226"
---
# <a name="long-term-retention---azure-sql-database-and-azure-sql-managed-instance"></a>Retenção a longo prazo - Azure SQL Database e Azure SQL Managed Instance

Muitas aplicações têm fins regulamentares, de conformidade ou outros fins comerciais que exigem que mantenha cópias de dados de base para além dos 7-35 dias fornecidos pela Azure SQL Database e pela Azure SQL Managed Instance [cópias de segurança automáticas.](automated-backups-overview.md) Ao utilizar a funcionalidade de retenção de longo prazo (LTR), pode armazenar cópias de dados completas de SQL e SQL Managed Instance no armazenamento Azure Blob com [redundância configurada](automated-backups-overview.md#backup-storage-redundancy) por um período máximo de 10 anos. Em seguida, pode restaurar qualquer cópia de segurança como uma nova base de dados.

A retenção de longo tempo pode ser ativada para Azure SQL Database, e está em pré-visualização pública limitada para Azure SQL Managed Instance. Este artigo fornece uma visão geral conceptual da retenção a longo prazo. Para configurar a retenção a longo prazo, consulte [Configure Azure SQL Database LTR](long-term-backup-retention-configure.md) e [Configure Azure SQL Managed Instance LTR](../managed-instance/long-term-backup-retention-configure.md). 

> [!NOTE]
> Pode utilizar os trabalhos do SqL Agent para agendar [cópias de segurança na base de dados apenas](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) como alternativa à LTR para além de 35 dias.


## <a name="how-long-term-retention-works"></a>Como funciona a retenção a longo prazo
     
A retenção de backup a longo prazo (LTR) aproveita todas as cópias de segurança da base de dados que são [criadas automaticamente](automated-backups-overview.md) para permitir a restauração do tempo de pontuação (PITR). Se uma política LTR estiver configurada, estas cópias de segurança são copiadas para diferentes bolhas para armazenamento a longo prazo. A cópia é um trabalho de fundo que não tem impacto no desempenho na carga de trabalho da base de dados. A política LTR para cada base de dados na Base de Dados SQL também pode especificar com que frequência são criadas as cópias de segurança LTR.

Para ativar o LTR, pode definir uma política utilizando uma combinação de quatro parâmetros: retenção semanal de backup (W), retenção mensal de backup (M), retenção anual de backup (Y) e semana do ano (WeekOfYear). Se especificar W, uma cópia de segurança por semana será copiada para o armazenamento a longo prazo. Se especificar M, a primeira cópia de segurança de cada mês será copiada para o armazenamento a longo prazo. Se especificar Y, uma cópia de segurança durante a semana especificada pela WeekOfYear será copiada para o armazenamento a longo prazo. Se a Semana Do Ano especificada for no passado quando a política estiver configurada, o primeiro backup LTR será criado no ano seguinte. Cada cópia de segurança será mantida no armazenamento a longo prazo de acordo com os parâmetros de política configurados quando a cópia de segurança LTR é criada.

> [!NOTE]
> Qualquer alteração à política LTR aplica-se apenas a futuras cópias de segurança. Por exemplo, se a retenção semanal de backup (W), a retenção mensal de backup (M) ou a retenção anual de backup (Y) forem modificadas, a nova definição de retenção só se aplicará a novas cópias de segurança. A retenção de cópias de segurança existentes não será modificada. Se a sua intenção for eliminar cópias de segurança LTR antigas antes do termo do período de retenção, terá de [eliminar manualmente as cópias de segurança](https://docs.microsoft.com/azure/sql-database/sql-database-long-term-backup-retention-configure#delete-ltr-backups).
> 

Exemplos da política LTR:

-  W=0, M=0, Y=5, WeekOfYear=3

   O terceiro reforço completo de cada ano será mantido por cinco anos.
   
- W=0, M=3, Y=0

   A primeira cópia de segurança completa de cada mês será mantida por três meses.

- W=12, M=0, Y=0

   Cada cópia de segurança completa semanal será mantida por 12 semanas.

- W=6, M=12, Y=10, WeekOfYear=16

   Cada reserva semanal completa será mantida por seis semanas. Exceto a primeira cópia de segurança completa de cada mês, que será mantida por 12 meses. Exceto o reforço completo da 16ª semana do ano, que será mantido por 10 anos. 

A tabela a seguir ilustra a cadência e a caducidade das cópias de segurança a longo prazo para a seguinte política:

W=12 semanas (84 dias), M=12 meses (365 dias), Y=10 anos (3650 dias), WeekOfYear=15 (semana após 15 de abril)

   ![exemplo ltr](./media/long-term-retention-overview/ltr-example.png)


Se modificar a política acima e definir W=0 (sem cópias de segurança semanais), a cadência de cópias de backup mudará conforme mostrado na tabela acima pelas datas realçadas. A quantidade de armazenamento necessária para manter estas cópias de segurança reduziria em conformidade. 

> [!IMPORTANT]
> O tempo de cópias de segurança LTR individuais é controlado pela Azure. Não é possível criar manualmente uma cópia de segurança LTR ou controlar o tempo da criação de cópias de segurança. Depois de configurar uma política LTR, pode demorar até 7 dias até que o primeiro backup LTR apareça na lista de cópias de segurança disponíveis.  


## <a name="geo-replication-and-long-term-backup-retention"></a>Geo-replicação e retenção de backup a longo prazo

Se estiver a utilizar grupos de geo-replicação ou de failover ativos como solução de continuidade do negócio, deve preparar-se para eventuais falhas e configurar a mesma política LTR na base de dados ou instâncias secundárias. O custo de armazenamento do LTR não aumentará, uma vez que as cópias de segurança não são geradas a partir dos secundários. As cópias de segurança só são criadas quando o secundário se torna primário, os backups serão criados. Garante a geração não interrompida dos backups LTR quando a falha é desencadeada e a primeira se move para a região secundária. 

> [!NOTE]
> Quando a base de dados primária original recuperar de uma paragem que causou a falência, tornar-se-á um novo secundário. Por conseguinte, a criação de backup não será retomada e a atual política ltr não entrará em vigor até que se torne novamente a principal. 

## <a name="sql-managed-instance-support"></a>Suporte ao SQL Managed Instance

A utilização de retenção de backup a longo prazo com Azure SQL Managed Instance tem as seguintes limitações:

- **Pré-visualização pública limitada** - Esta pré-visualização só está disponível para assinaturas EA e CSP e está sujeita a disponibilidade limitada.  
- [**Apenas PowerShell**](../managed-instance/long-term-backup-retention-configure.md) - Não existe atualmente suporte ao portal Azure. O LTR deve ser ativado utilizando o PowerShell. 

Para solicitar a inscrição, crie um [bilhete de apoio Azure.](https://azure.microsoft.com/support/create-ticket/) Para o tipo de problema selecione problema técnico, para o serviço escolha SQL Caso Gerido, e para o tipo de problema **selecione Backup, Restore e Business continuidade / Retenção de backup a longo prazo**. No seu pedido, por favor, diga que gostaria de ser inscrito numa pré-visualização pública limitada de LTR para SQL Managed Instance.

## <a name="configure-long-term-backup-retention"></a>Configurar a retenção de cópias de segurança de longa duração

Pode configurar a retenção de backup a longo prazo utilizando o portal Azure e PowerShell para Azure SQL Database e PowerShell para Azure SQL Managed Instance. Para restaurar uma base de dados a partir do armazenamento LTR, pode selecionar uma cópia de segurança específica com base na sua hora de tempo. A base de dados pode ser restaurada em qualquer servidor existente ou caso gerido sob a mesma subscrição que a base de dados original.

Para aprender a configurar a retenção a longo prazo ou restaurar uma base de dados a partir de backup para base de dados SQL utilizando o portal Azure ou PowerShell, consulte [Gerir a rede de backup de longo prazo da Manage Azure SQL Database](long-term-backup-retention-configure.md)

Para aprender a configurar a retenção a longo prazo ou restaurar uma base de dados a partir de backup para SQL Managed Instance usando PowerShell, consulte [Gerir a gestão de Azure SQL Gestão de Instâncias de segurança a longo prazo](../managed-instance/long-term-backup-retention-configure.md).

Para restaurar uma base de dados a partir do armazenamento LTR, pode selecionar uma cópia de segurança específica com base na sua hora de tempo. A base de dados pode ser restaurada em qualquer servidor existente sob a mesma subscrição que a base de dados original. Para aprender a restaurar a sua base de dados a partir de uma cópia de segurança LTR, utilizando o portal Azure, ou PowerShell, consulte [Gerir a rede de backup de longo prazo da Manage Azure SQL Database](long-term-backup-retention-configure.md). No seu pedido, por favor, diga que gostaria de estar inscrito na antevisão pública limitada do LTR para SQL Managed Instance.

## <a name="next-steps"></a>Passos seguintes

Como as cópias de dados protegem os dados da corrupção acidental ou da eliminação, são uma parte essencial de qualquer estratégia de continuidade de negócios e recuperação de desastres. Para conhecer as outras soluções de continuidade de negócios da SQL Database, consulte [a visão geral da continuidade do Negócio.](business-continuity-high-availability-disaster-recover-hadr-overview.md)
 
