---
title: Atualizar a aplicação Recoletora no Azure Migrate | Documentos da Microsoft
description: Fornece informações sobre as atualizações para a aplicação de Recoletor do Azure Migrate.
author: musa-57
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: hamusa
services: azure-migrate
ms.openlocfilehash: d7a9a6cb8683116b9e0c432c2aede2b8df3ddc68
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57895636"
---
# <a name="collector-update-release-history"></a>Histórico de lançamento de atualização do recoletor

Este artigo resume as informações de atualização para a aplicação de Recoletor na [do Azure Migrate](migrate-overview.md).

Recoletor do Azure Migrate é uma aplicação simples que é utilizada para detetar um ambiente no local do vCenter, para fins de avaliação antes da migração para o Azure. [Saiba mais](concepts-collector.md).

## <a name="continuous-discovery-upgrade-versions"></a>Deteção contínua: Versões de atualização

### <a name="version-101012-released-on-03132019"></a>Versão 1.0.10.12 (lançado em 03/13/2019)

Contém correções para problemas na seleção do Azure na cloud a aplicação.

Valores para a atualização de hash [1.0.10.12 do pacote](https://aka.ms/migrate/col/upgrade_10_12)

**Algoritmo** | **Valor de hash**
--- | ---
MD5 | 27704154082344c058238000dff9ae44
SHA1 | 41e9e2fb71a8dac14d64f91f0fd780e0d606785e
SHA256 | c6e7504fcda46908b636bfe25b8c73f067e3465b748f77e50027e66f2727c2a9

## <a name="one-time-discovery-deprecated-now-previous-upgrade-versions"></a>Deteção única (agora preterida): Versões de atualização anteriores

> [!NOTE]
> A aplicação de deteção de uso individual foi agora preterida como esse método baseou-se no vCenter definições de estatísticas do servidor para a disponibilidade de ponto de dados de desempenho e coletados contadores de desempenho médio que resultou em insuficientemente dimensionamento de VMs para a migração para o Azure.

### <a name="version-10916-released-on-10292018"></a>Versão 1.0.9.16 (lançado em 10/29/2018)

Contém correções para problemas do PowerCLI enfrentados ao configurar a aplicação.

Valores para a atualização de hash [1.0.9.16 do pacote](https://aka.ms/migrate/col/upgrade_9_16)

**Algoritmo** | **Valor de hash**
--- | ---
MD5 | d2c53f683b0ec7aaf5ba3d532a7382e1
SHA1 | e5f922a725d81026fa113b0c27da185911942a01
SHA256 | a159063ff508e86b4b3b7b9a42d724262ec0f2315bdba8418bce95d973f80cfc

### <a name="version-10914"></a>Versão 1.0.9.14

Valores para a atualização de hash [1.0.9.14 do pacote](https://aka.ms/migrate/col/upgrade_9_14)

**Algoritmo** | **Valor de hash**
--- | ---
MD5 | c5bf029e9fac682c6b85078a61c5c79c
SHA1 | af66656951105e42680dfcc3ec3abd3f4da8fdec
SHA256 | 58b685b2707f273aa76f2e1d45f97b0543a8c4d017cd27f0bdb220e6984cc90e

### <a name="version-10913"></a>Versão 1.0.9.13

Valores para a atualização de hash [1.0.9.13 do pacote](https://aka.ms/migrate/col/upgrade_9_13)

**Algoritmo** | **Valor de hash**
--- | ---
MD5 | 739f588fe7fb95ce2a9b6b4d0bf9917e
SHA1 | 9b3365acad038eb1c62ca2b2de1467cb8eed37f6
SHA256 | 7a49fb8286595f39a29085534f29a623ec2edb12a3d76f90c9654b2f69eef87e


## <a name="run-an-upgrade"></a>Executar uma atualização

Pode atualizar o Recoletor para a versão mais recente sem baixar o OVA novamente.

1. Transferir o pacote de atualização mais recente na lista abaixo.
2. Para garantir que a correção transferida é segura, abra a janela de comando de administrador e execute o seguinte comando para gerar o hash para o ficheiro ZIP. O hash gerado deve corresponder com o hash mencionado em relação a versão específica:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    Exemplo: **C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)**
3. Copie o ficheiro zip para a aplicação Recoletora VM.
4. Com o botão direito no ficheiro zip > **extrair tudo**.
5. Com o botão direito no **Setup.ps1** > **executar com o PowerShell**e siga as instruções de instalação.


## <a name="next-steps"></a>Passos Seguintes

- [Saiba mais](concepts-collector.md) sobre a aplicação Recoletora.
- [Execute uma avaliação](tutorial-assessment-vmware.md) para VMs de VMware.
