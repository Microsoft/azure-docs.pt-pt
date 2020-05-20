---
title: Mova um cofre chave para outra região - Azure Key Vault [ Azure Key Vault ] Microsoft Docs
description: Este artigo oferece orientação sobre a mudança de um cofre chave para uma região diferente.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another region.
ms.openlocfilehash: 4f9f43b3d0aa0af8696300933c08c140951e5e52
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83651220"
---
# <a name="move-an-azure-key-vault-across-regions"></a>Mova um cofre chave Azure através das regiões

O Cofre Chave Azure não suporta uma operação de movimento de recursos que permite mover um cofre chave de uma região para outra. Este artigo abrange sobras para organizações que têm uma necessidade de mover um cofre chave para outra região. Cada opção de suver tem limitações. É fundamental compreender as implicações destas sobras antes de tentar aplicá-las num ambiente de produção.

Para mover um cofre chave para outra região, você cria um cofre chave nessa outra região e, em seguida, copia manualmente cada segredo individual do seu cofre chave existente para o novo cofre chave. Pode fazê-lo utilizando qualquer uma das duas opções seguintes.

## <a name="design-considerations"></a>Considerações de conceção

Antes de começar, tenha em mente os seguintes conceitos:

* Os nomes do cofre são globalmente únicos. Não pode reutilizar o nome do cofre.
* Tem de reconfigurar as suas políticas de acesso e configurações de configuração de rede no novo cofre de chaves.
* É necessário reconfigurar a proteção de eliminação suave e purgar a proteção no novo cofre chave.
* A operação de backup e restauro não preservará as definições de rotação automática. Pode ser necessário reconfigurar as definições.

## <a name="option-1-use-the-key-vault-backup-and-restore-commands"></a>Opção 1: Utilize a cópia de segurança do cofre chave e restaure os comandos

Pode fazer backup de cada segredo individual, chave e certificado no seu cofre usando o comando de reserva. Os teus segredos são descarregados como uma bolha encriptada. Pode então restaurar a bolha no seu novo cofre chave. Para obter uma lista de comandos, consulte os [comandos do Cofre chave Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-6.13.0#key_vault).

A utilização dos comandos de backup e restauro tem duas limitações:

* Não se pode apoiar um cofre chave numa geografia e restaurá-lo noutra geografia. Para mais informações, consulte [geografias azure.](https://azure.microsoft.com/global-infrastructure/geographies/)

* O comando de reserva apoia todas as versões de cada segredo. Se tiver um segredo com um grande número de versões anteriores (mais de 10), o tamanho do pedido pode exceder o máximo permitido e a operação pode falhar.

## <a name="option-2-manually-download-and-upload-the-key-vault-secrets"></a>Opção 2: Descarregue manualmente e carregue os segredos do cofre chave

Pode descarregar manualmente certos tipos secretos. Por exemplo, pode descarregar certificados como um ficheiro PFX. Esta opção elimina as restrições geográficas para alguns tipos secretos, tais como certificados. Pode enviar os ficheiros PFX para qualquer cofre chave em qualquer região. Os segredos são descarregados num formato protegido sem palavra-passe. É responsável por assegurar os seus segredos durante a mudança.
