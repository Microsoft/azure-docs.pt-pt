---
title: Cofre chave Azure movendo um cofre para outra região Microsoft Docs
description: Orientação sobre mover um cofre chave para outra região.
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
ms.openlocfilehash: e65a723ac9daafdc09896a50e197034104408df2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82254151"
---
# <a name="moving-an-azure-key-vault-across-regions"></a>Movendo um cofre de chaves azure através de regiões

## <a name="overview"></a>Descrição geral

O Cofre Chave não suporta uma operação de movimento de recursos que permite mover um cofre chave para outra região. Este artigo cobrirá sobras se tiver um negócio necessário para mover um cofre chave para outra região. Cada opção tem limitações e é fundamental compreender as implicações destas salções antes de as tentar num ambiente de produção.

Se precisar de mover um cofre chave para outra região, a solução é criar um novo cofre chave na região desejada e copiar manualmente cada segredo individual do seu cofre chave existente para o novo cofre chave. Esta operação pode ser feita em qualquer uma das seguintes formas listadas abaixo.

## <a name="design-considerations"></a>Considerações sobre Design

* Os nomes do cofre são globalmente únicos. Não poderá reutilizar o mesmo nome do cofre.

* Terá de reconfigurar as políticas de acesso e configurações de configuração da rede no novo cofre chave.

* Terá de reconfigurar a proteção de eliminação suave e purgar a proteção no novo cofre chave.

* O funcionamento de back up e restauro não preservará as definições de rotação automática que poderá necessitar para reconfigurar estas definições.

## <a name="option-1---use-the-key-vault-backup-and-restore-commands"></a>Opção 1 - Utilize a cópia de segurança do cofre chave e restaure os comandos

Pode suster cada segredo individual, chave e certificado no seu cofre usando o comando de reserva. Os seus segredos serão descarregados como uma bolha encriptada. Pode então restaurar a bolha no seu novo cofre chave. Os comandos estão documentados no link abaixo.

[Comandos do cofre de chaves Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-6.13.0#key_vault)

### <a name="limitations"></a>Limitações

* Não se pode apoiar um cofre chave numa geografia e restaurá-lo noutra geografia. Saiba mais sobre geografias Azure. [Ligação](https://azure.microsoft.com/global-infrastructure/geographies/)

* O comando de reserva apoia todas as versões de cada segredo. Se tiver um segredo com um grande número de versões anteriores (superiora a 10) existe a possibilidade de o pedido exceder o tamanho máximo permitido do pedido e a operação poder falhar.

## <a name="option-2---manually-download-and-upload-secrets"></a>Opção 2 - Transferir manualmente e carregar segredos

Certos tipos secretos podem ser descarregados manualmente. Por exemplo, pode baixar certificados como ficheiro .pfx. Esta opção elimina as restrições geográficas para alguns tipos secretos, como certificados. Pode enviar os ficheiros .pfx para qualquer cofre chave em qualquer região. O seu segredo será descarregado num formato protegido sem palavra-passe. Será responsável por assegurar os seus segredos assim que saírem do Cofre chave enquanto a mudança é executada.
