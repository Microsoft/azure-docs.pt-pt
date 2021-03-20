---
title: Mova um cofre chave para uma região diferente - Azure Key Vault | Microsoft Docs
description: Este artigo oferece orientação sobre a mudança de um cofre chave para uma região diferente.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/24/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another region.
ms.openlocfilehash: 54a1894c88d12cfac1a2d8062425de9549c4292e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93285200"
---
# <a name="move-an-azure-key-vault-across-regions"></a>Mova um cofre chave Azure através de regiões

O Azure Key Vault não suporta uma operação de movimento de recursos que permita mover um cofre chave de uma região para outra. Este artigo abrange soluções alternativas para organizações que têm uma necessidade de mudar um cofre chave para outra região. Cada opção de solução tem limitações. É fundamental compreender as implicações destas soluções antes de tentar aplicá-las num ambiente de produção.

Para mover um cofre chave para outra região, você cria um cofre chave nessa outra região e, em seguida, copia manualmente cada segredo individual do seu cofre chave existente para o novo cofre chave. Pode fazê-lo utilizando qualquer uma das duas opções seguintes.

## <a name="design-considerations"></a>Considerações de conceção

Antes de começar, lembre-se dos seguintes conceitos:

* Os nomes do cofre são globalmente únicos. Não pode reutilizar um nome de cofre.
* É necessário reconfigurar as suas políticas de acesso e as definições de configuração da rede no novo cofre de chaves.
* É necessário reconfigurar a proteção de eliminação e purga no novo cofre da chave.
* A operação de backup e restauro não preservará as definições de autorotação. Pode ser necessário reconfigurar as definições.

## <a name="option-1-use-the-key-vault-backup-and-restore-commands"></a>Opção 1: Use a cópia de segurança do cofre e restaure os comandos

Pode apoiar cada segredo, chave e certificado individual no seu cofre usando o comando de reserva. Os seus segredos são descarregados como uma bolha encriptada. Em seguida, pode restaurar a bolha no seu novo cofre de chaves. Para obter uma lista de comandos, consulte os [comandos do Azure Key Vault](/powershell/module/azurerm.keyvault/?view=azurermps-6.13.0#key_vault).

A utilização dos comandos de backup e restauro tem duas limitações:

* Não se pode apoiar um cofre chave numa geografia e restaurá-lo noutra geografia. Para mais informações, consulte [as geografias Azure.](https://azure.microsoft.com/global-infrastructure/geographies/)

* O comando de cópia de segurança faz a cópia de segurança de todas as versões de cada segredo. Se tiver um segredo com um grande número de versões anteriores (mais de 10), o tamanho do pedido poderá exceder o máximo permitido e a operação poderá falhar.

## <a name="option-2-manually-download-and-upload-the-key-vault-secrets"></a>Opção 2: Descarregar manualmente e carregar os segredos do cofre chave

Você pode baixar certos tipos secretos manualmente. Por exemplo, pode baixar certificados como um ficheiro PFX. Esta opção elimina as restrições geográficas para alguns tipos secretos, tais como certificados. Pode enviar os ficheiros PFX para qualquer cofre chave em qualquer região. Os segredos são descarregados num formato não protegido por palavra-passe. É responsável por proteger os seus segredos durante a mudança.