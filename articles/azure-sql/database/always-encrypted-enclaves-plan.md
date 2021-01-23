---
title: Plano para enclaves e atestado intel SGX na Base de Dados Azure SQL
description: Planeie a implantação de Always Encrypted com enclaves seguros na Base de Dados Azure SQL.
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
ms.openlocfilehash: 4448ce051b0c9e73865e8057cc4f224c9cbeb571
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98732749"
---
# <a name="plan-for-intel-sgx-enclaves-and-attestation-in-azure-sql-database"></a>Plano para enclaves e atestado intel SGX na Base de Dados Azure SQL

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Sempre encriptado com enclaves seguros para Azure SQL Database está atualmente em **pré-visualização pública**.

[Sempre encriptado com enclaves seguros](/sql/relational-databases/security/encryption/always-encrypted-enclaves) na Base de Dados Azure SQL utiliza [enclaves de extensões de proteção de software intel (Intel SGX)](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/) e requer [a Attestation Microsoft Azure](/sql/relational-databases/security/encryption/always-encrypted-enclaves#secure-enclave-attestation).

## <a name="plan-for-intel-sgx-in-azure-sql-database"></a>Plano para Intel SGX em Base de Dados Azure SQL

Intel SGX é uma tecnologia de ambiente de execução de confiança baseada em hardware. O Intel SGX está disponível para bases de dados que utilizam o [modelo vCore](service-tiers-vcore.md) e a geração de hardware da [série DC.](service-tiers-vcore.md?#dc-series) Portanto, para garantir que pode utilizar Sempre Encriptado com enclaves seguros na sua base de dados, precisa de selecionar a geração de hardware da série DC quando criar a base de dados, ou pode atualizar a sua base de dados existente para utilizar a geração de hardware da série DC.

> [!NOTE]
> A Intel SGX não está disponível em gerações de hardware que não a série DC. Por exemplo, o Intel SGX não está disponível para hardware da Gen5, e não está disponível para bases de dados utilizando o [modelo DTU](service-tiers-dtu.md).

> [!IMPORTANT]
> Antes de configurar a geração de hardware da série DC para a sua base de dados, verifique a disponibilidade regional da série DC e certifique-se de que compreende as suas limitações de desempenho. Para mais detalhes, consulte [a série DC](service-tiers-vcore.md#dc-series).

## <a name="plan-for-attestation-in-azure-sql-database"></a>Plano para atestado na Base de Dados Azure SQL

[O Microsoft Azure Attestation](../../attestation/overview.md) (pré-visualização) é uma solução para atestar Ambientes de Execução Fidedigna (TEEs), incluindo enclaves intel SGX em bases de dados Azure SQL usando a geração de hardware da série DC.

Para utilizar o Azure Attestation para atestar enclaves intel SGX na Base de Dados Azure SQL, você precisa:

1. Crie um [fornecedor de atestado e](../../attestation/basic-concepts.md#attestation-provider) configuure-o com uma política de atestação. 

2. Conceda ao seu servidor lógico Azure SQL acesso ao fornecedor de atestado criado.

## <a name="roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation"></a>Funções e responsabilidades ao configurar enclaves e atestados SGX

Configurar o seu ambiente para suportar enclaves intel SGX e atestado para Sempre Encriptado na Base de Dados SQL Azure envolve a configuração de componentes de diferentes tipos: Microsoft Azure Attestation, Azure SQL Database, e aplicações que desencadeiam atestado de enclave. A configuração dos componentes de cada tipo é realizada pelos utilizadores assumindo uma das funções abaixo distintas:

- Administrador de attestation - cria um fornecedor de atestado no Microsoft Azure Attestation, autores da política de atestado, concede acesso lógico ao servidor lógico Azure SQL ao fornecedor de atestado, e partilha o URL de atestado que aponta para a política aos administradores de aplicações.
- Administrador de Base de Dados Azure SQL - permite enclaves SGX em bases de dados selecionando a geração de hardware da série DC, e fornece ao administrador de atestado a identidade do servidor lógico Azure SQL que precisa de aceder ao fornecedor de atestado.
- Administrador de aplicação - configura aplicações com o URL de atestado obtido pelo administrador da atestado.

Em ambientes de produção (manuseamento de dados sensíveis reais), é importante que a sua organização adere à separação de papéis ao configurar a atestação, onde cada papel distinto é assumido por diferentes pessoas. Em particular, se o objetivo de implementar Sempre Encriptado na sua organização é reduzir a área de superfície de ataque, garantindo que os administradores da Base de Dados Azure SQL não possam aceder a dados sensíveis, os administradores da Base de Dados Azure SQL não devem controlar as políticas de atestação.

## <a name="next-steps"></a>Próximos passos

- [Ativar a Intel SGX para a sua base de dados Azure SQL](always-encrypted-enclaves-enable-sgx.md)

## <a name="see-also"></a>Veja também

- [Tutorial: Começar com Sempre Encriptado com enclaves seguros na Base de Dados Azure SQL](always-encrypted-enclaves-getting-started.md)