---
title: 'Gerar e exportar certificados para ligações ponto a Site: Linux: CLI: Azure | Microsoft Docs'
description: Criar um certificado de raiz autoassinado, exportar a chave pública e gerar certificados de cliente com a Linux (strongSwan) da CLI.
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: article
origin.date: 01/31/2019
ms.date: 03/04/2019
ms.author: v-jay
ms.openlocfilehash: b673be47d4951adab08f04efc56410095f549356
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60766179"
---
# <a name="generate-and-export-certificates"></a>Gerar e exportar certificados

As ligações ponto a Site utilizam certificados para autenticar. Este artigo mostra-lhe como criar um certificado de raiz autoassinados e gerar certificados de cliente utilizando a CLI de Linux e strongSwan. Se estiver à procura para obter instruções de certificado diferente, consulte a [Powershell](vpn-gateway-certificates-point-to-site.md) ou [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) artigos. Para obter informações sobre como instalar strongSwan usando a GUI em vez da CLI, veja os passos a [configuração do cliente](point-to-site-vpn-client-configuration-azure-cert.md#install) artigo.

## <a name="generate-and-export"></a>Gerar e exportar
[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>Passos Seguintes

Continuar com a sua configuração de ponto a Site para [criar e instalar ficheiros de configuração de cliente VPN](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).