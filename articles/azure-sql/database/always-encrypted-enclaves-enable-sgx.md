---
title: Ativar a Intel SGX para a sua Base de Dados Azure SQL
description: Saiba como ativar o Intel SGX para Sempre Encriptado com enclaves seguros na Base de Dados Azure SQL selecionando uma geração de hardware ativada por SGX.
keywords: encriptar dados, encriptação sql, encriptação de base de dados, dados confidenciais, Sempre Criptografados, enclaves seguros, SGX, atestado
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: 4c77103dbb043ef9d6af9a4078b3e574ab5f953f
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253460"
---
# <a name="enable-intel-sgx-for-your-azure-sql-database"></a>Ativar a Intel SGX para a sua Base de Dados Azure SQL 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Sempre encriptado com enclaves seguros para Azure SQL Database está atualmente em **pré-visualização pública**.

[Sempre encriptado com enclaves seguros](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-enclaves) na Base de Dados Azure SQL utiliza [enclaves de extensões de guarda de software intel (Intel SGX).](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/) Para que a Intel SGX esteja disponível, a base de dados deve utilizar o [modelo vCore](service-tiers-vcore.md) e a geração de hardware da [série DC.](service-tiers-vcore.md#dc-series)

Configurar a geração de hardware da série DC para permitir enclaves intel SGX é da responsabilidade do administrador da Base de Dados Azure SQL. Ver [Papéis e responsabilidades ao configurar enclaves EGX e atestado](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation).

> [!NOTE]
> A Intel SGX não está disponível em gerações de hardware que não a série DC. Por exemplo, o Intel SGX não está disponível para hardware da Gen5, e não está disponível para bases de dados utilizando o [modelo DTU](service-tiers-dtu.md).

> [!IMPORTANT]
> Antes de configurar a geração de hardware da série DC para a sua base de dados, verifique a disponibilidade regional da série DC e certifique-se de que compreende as suas limitações de desempenho. Para mais informações, consulte [a série DC.](service-tiers-vcore.md#dc-series)

Para obter instruções detalhadas sobre como configurar uma base de dados nova ou existente para utilizar uma geração de hardware específica, consulte [Selecionar uma geração de hardware](service-tiers-vcore.md#selecting-a-hardware-generation).
   
## <a name="next-steps"></a>Passos seguintes

- [Configure Azure Attestation para o seu servidor de base de dados Azure SQL](always-encrypted-enclaves-configure-attestation.md)

## <a name="see-also"></a>Ver também

- [Tutorial: Começar com Sempre Encriptado com enclaves seguros na Base de Dados Azure SQL](always-encrypted-enclaves-getting-started.md)