---
title: Matriz de suporte de backup Azure para backup do servidor SQL em VMs Azure
description: Fornece um resumo das definições e limitações de suporte ao fazer o backup do SQL Server em VMs Azure com o serviço de Backup Azure.
ms.topic: conceptual
ms.date: 04/07/2021
ms.custom: references_regions
ms.openlocfilehash: 354f64eb86cd545860c47562fba7ff43babe72ca
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714151"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>Matriz de suporte para backup do servidor SQL em VMs Azure

Pode utilizar o Azure Backup para fazer backup nas bases de dados do SQL Server em VMs Azure hospedados na plataforma cloud microsoft Azure. Este artigo resume as configurações e limitações gerais de suporte para cenários e implementações de Backup do Servidor SQL em VMs Azure.

## <a name="scenario-support"></a>Suporte de cenário

**Suporte** | **Detalhes**
--- | ---
**Implantações apoiadas** | Sql Marketplace Azure VMs e VMs não-Marketplace (SQL Server instalado manualmente) são suportados.
**Regiões suportadas** | Austrália Sudeste (ASE), Austrália Oriental (AE), Austrália Central (AC), Austrália Central 2 (AC) <br> Sul do Brasil (BRS)<br> Canadá Central (CNC), Canadá Leste (CE)<br> Sudeste Asiático (SEA), Ásia Oriental (EA) <br> Leste dos EUA (EUS), Leste dos EUA 2 (EUS2), Centro Ocidental dos EUA (WCUS), Eua Ocidental (WUS); West US 2 (WUS 2) North Central US (NCUS) Central US (CUS) South Central US (SCUS) <br> India Central (INC), Índia Sul (INS), Índia Oeste <br> Japão Leste (JPE), Japão Oeste (JPW) <br> Coreia Central (KRC), Coreia do Sul (KRS) <br> Norte da Europa (NE), Europa Ocidental <br> Reino Unido Sul (Reino Unido), UK West (UKW) <br> US Gov Arizona, US Gov Virginia, US Gov Texas, US DoD Central, US DoD East <br> Alemanha Norte, Alemanha Centro Ocidental <br> Suíça Norte, Suíça Oeste <br> França Central <br> China Leste, China Leste 2, China Norte, China Norte 2
**Sistemas operativos suportados** | Windows Server 2019, Windows Server 2016, Windows Server 2012, Windows Server 2008 R2 SP1 <br/><br/> Linux não é apoiado.
**Versões suportadas do SQL Server** | SQL Server 2019, SQL Server 2017 conforme detalhado na [página de lifecycle](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017)do produto Search , SQL Server 2016 e SPs conforme detalhado na [página de lifecycle do produto Search](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack), SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008 <br/><br/> Empresa, Standard, Web, Developer, Express.<br><br>As versões DB locais do Expresso não são suportadas.
**Versões suportadas .NET** | .NET Quadro 4.5.2 ou posteriormente instalado no VM

## <a name="feature-considerations-and-limitations"></a>Considerações e limitações de recursos

|Definição  |Limite máximo |
|---------|---------|
|Número de bases de dados que podem ser protegidas num servidor (e num cofre)    |   2000      |
|Tamanho da base de dados suportado (para além disso, problemas de desempenho podem surgir)   |   6 TB*      |
|Número de ficheiros suportados numa base de dados    |   1000      |

_*O limite de tamanho da base de dados depende da taxa de transferência de dados que suportamos e da configuração do prazo de backup. Não é o limite difícil. [Saiba mais](#backup-throughput-performance) sobre o desempenho do backup._

* A cópia de segurança do SQL Server pode ser configurada no portal Azure ou **no PowerShell**. O CLI não é apoiado.
* A solução é suportada em ambos os tipos de [implementações](../azure-resource-manager/management/deployment-models.md) - VMs Azure Resource Manager e VMs clássicos.
* Todos os tipos de backup (full/diferencial/log) e modelos de recuperação (simples/full/granel) são suportados.
* Para bases de dados **apenas para leitura:** cópias de segurança completas e apenas cópias são os únicos tipos de backup suportados.
* A compressão nativa SQL é suportada se for ativada explicitamente pelo utilizador na política de backup. O Azure Backup substitui os padrãos de nível de instância com a cláusula COMPRESSION/NO_COMPRESSION, dependendo do valor deste controlo tal como definido pelo utilizador.
* TDE - a cópia de segurança da base de dados ativada é suportada. Para restaurar uma base de dados encriptada TDE para outro SqL Server, é necessário restaurar primeiro [o certificado no servidor de destino](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server). A compressão de backup para bases de dados ativadas pelo TDE para o SQL Server 2016 e versões mais recentes estão disponíveis, mas com menor tamanho de transferência, como explicado [aqui.](https://techcommunity.microsoft.com/t5/sql-server/backup-compression-for-tde-enabled-databases-important-fixes-in/ba-p/385593)
* As operações de backup e restauro para bases de dados de espelhos e imagens de bases de dados não são suportadas.
* SQL Server **Failover Cluster Instance (FCI)** não é suportado.
* Utilizar mais de uma solução de backup para fazer backup da sua instância autónoma do SQL Server ou SQL Sempre no grupo de disponibilidade pode levar a falhas de backup. Abstenha-se de fazê-lo. O backup de dois nós de um grupo de disponibilidade individualmente com soluções iguais ou diferentes, também pode levar a falhas de backup.
* Quando os grupos de disponibilidade são configurados, as cópias de segurança são retiradas dos diferentes nós com base em alguns fatores. O comportamento de backup para um grupo de disponibilidade é resumido abaixo.

### <a name="back-up-behavior-with-always-on-availability-groups"></a>Comportamento da cópia de segurança com grupos de disponibilidade Always On

Recomendamos que a cópia de segurança seja configurada apenas num nó de um grupo de disponibilidade (AG). Configuure sempre o backup na mesma região que o nó primário. Por outras palavras, precisa sempre do nó primário para estar presente na região onde está a configurar o backup. Se todos os nós da AG estão na mesma região onde o backup está configurado, não há nenhuma preocupação.

#### <a name="for-cross-region-ag"></a>Para a AG transversal

* Independentemente da preferência de backup, as cópias de segurança só irão funcionar a partir dos nós que estão na mesma região onde a cópia de segurança está configurada. Isto porque os reforços transversais não são apoiados. Se tiver apenas dois nóns e o nó secundário estiver na outra região, os backups continuarão a funcionar a partir do nó primário (a menos que a sua preferência de backup seja 'apenas secundária').
* Se um nó falhar numa região diferente daquela em que o backup está configurado, os backups falharão nos nós na região falhada.

Dependendo da preferência de backup e dos tipos de backups (completo/diferencial/log/copy-only full), as cópias são retiradas de um nó particular (primário/secundário).

#### <a name="backup-preference-primary"></a>Preferência de backup: Primário

**Tipo de Cópia de Segurança** | **Nó**
--- | ---
Completa | Primário
Diferencial | Primário
Registo |  Primário
Copy-Only Cheio |  Primário

#### <a name="backup-preference-secondary-only"></a>Preferência de backup: Apenas secundário

**Tipo de Cópia de Segurança** | **Nó**
--- | ---
Completa | Primário
Diferencial | Primário
Registo |  Secundária
Copy-Only Cheio |  Secundária

#### <a name="backup-preference-secondary"></a>Preferência de backup: Secundário

**Tipo de Cópia de Segurança** | **Nó**
--- | ---
Completa | Primário
Diferencial | Primário
Registo |  Secundária
Copy-Only Cheio |  Secundária

#### <a name="no-backup-preference"></a>Sem preferência de backup

**Tipo de Cópia de Segurança** | **Nó**
--- | ---
Completa | Primário
Diferencial | Primário
Registo |  Secundária
Copy-Only Cheio |  Secundária

## <a name="backup-throughput-performance"></a>Desempenho de produção de backup

O Azure Backup suporta uma taxa de transferência de dados consistente de 200 Mbps para cópias de segurança completas e diferenciais de grandes bases de dados SQL (de 500 GB). Para utilizar o desempenho ideal, certifique-se de que:

- O VM subjacente (que contém a instância SQL Server, que acolhe a base de dados) está configurado com a produção de rede necessária. Se o rendimento máximo do VM for inferior a 200 Mbps, o Azure Backup não pode transferir dados à velocidade ideal.<br>Além disso, o disco que contém os ficheiros da base de dados deve ter o suficiente de produção. [Saiba mais](../virtual-machines/disks-performance.md) sobre a produção de discos e desempenho em VMs Azure. 
- Os processos, que estão a decorrer no VM, não estão a consumir a largura de banda VM. 
- Os horários de reserva estão espalhados por um subconjunto de bases de dados. Várias cópias de segurança em execução simultânea num VM partilham a taxa de consumo de rede entre as cópias de segurança. [Saiba mais](faq-backup-sql-server.yml#can-i-control-how-many-concurrent-backups-run-on-the-sql-server-) sobre como controlar o número de backups simultâneos.

>[!NOTE]
> [Faça o download do Planejador de Recursos detalhado](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) para calcular o número aproximado de bases de dados protegidas que são recomendadas por servidor com base nos recursos VM, largura de banda e na política de backup.

## <a name="next-steps"></a>Passos seguintes

Saiba como [fazer o back up de uma base de dados](backup-azure-sql-database.md) do SQL Server que está a funcionar num VM Azure.