---
title: Central de segurança do Azure e registro de contêiner do Azure
description: Saiba mais sobre a integração da central de segurança do Azure com o registro de contêiner do Azure
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/19/2019
ms.author: memildin
ms.openlocfilehash: 4cc88e7c04d10907a9a6386b1266eb8031d60926
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552683"
---
# <a name="azure-container-registry-integration-with-security-center-preview"></a>Integração do registro de contêiner do Azure com a central de segurança (versão prévia)

O ACR (registro de contêiner do Azure) é um serviço de registro gerenciado e privado do Docker que armazena e gerencia suas imagens de contêiner para implantações do Azure em um registro central. Ele é baseado no registro do Docker 2,0 de código aberto.

Para obter uma visibilidade mais profunda das vulnerabilidades do registro e das imagens, os usuários da camada Standard da central de segurança do Azure podem habilitar o pacote de registros de contêiner opcionais. O custo para usar esse recurso é cobrado por imagem, não por verificação. Para obter mais informações, veja os [preços](security-center-pricing.md). Com o pacote habilitado, a central de segurança examina automaticamente as imagens no registro sempre que uma imagem é enviada por push ao registro.

> [!NOTE]
> A primeira verificação da central de segurança de um registro só ocorrerá depois que o grupo de registros de contêiner estiver habilitado e uma imagem for enviada por push ao registro.

Quando a verificação for concluída (normalmente após aproximadamente 10 minutos), as descobertas estarão disponíveis nas recomendações da central de segurança como esta:

[![exemplo de recomendação da central de segurança do Azure sobre vulnerabilidades descobertas em uma imagem hospedada do ACR (registro de contêiner do Azure)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Benefícios da integração

A central de segurança identifica registros de ACR em sua assinatura e fornece diretamente:

* **Azure-verificação de vulnerabilidades nativas** para todas as imagens do Linux enviadas por push. A central de segurança examina a imagem usando um scanner do fornecedor de verificação de vulnerabilidade líder do setor, Qualys. Essa solução nativa é totalmente integrada por padrão.

* **Recomendações de segurança** para imagens do Linux com vulnerabilidades conhecidas. A central de segurança fornece detalhes de cada vulnerabilidade relatada e uma classificação de gravidade. Além disso, ele fornece orientação sobre como corrigir as vulnerabilidades específicas encontradas em cada imagem enviada por push para o registro.

![Visão geral de alto nível da central de segurança do Azure e do ACR (registro de contêiner do Azure)](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os recursos de segurança do contêiner da central de segurança, confira:

* [Central de segurança do Azure e segurança do contêiner](container-security.md)

* [Integração com o serviço kubernetes do Azure](azure-kubernetes-service-integration.md)

* [Proteção da máquina virtual](security-center-virtual-machine-protection.md) – descreve as recomendações da central de segurança
