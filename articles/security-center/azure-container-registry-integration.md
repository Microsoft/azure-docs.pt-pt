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
ms.openlocfilehash: 2d588d2707c267097e25176997e58f9573017582
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780050"
---
# <a name="azure-container-registry-integration-with-security-center-preview"></a>Integração do registro de contêiner do Azure com a central de segurança (versão prévia)

O ACR (registro de contêiner do Azure) é um serviço de registro gerenciado e privado do Docker que armazena e gerencia suas imagens de contêiner para implantações do Azure em um registro central. Ele é baseado no registro do Docker 2,0 de código aberto.

Se você estiver na camada Standard da central de segurança do Azure, poderá adicionar o pacote de registros de contêiner. Esse recurso opcional traz uma visibilidade mais profunda das vulnerabilidades das imagens em seus registros. Habilite ou desabilite o pacote no nível de assinatura para abranger todos os registros em uma assinatura. Esse recurso é cobrado por imagem, não por verificação, conforme mostrado na [página de preços](security-center-pricing.md). 

Habilitando o pacote de registros de contêiner, o garante que a central de segurança esteja pronta para verificar imagens que são enviadas por push ao registro. As verificações estão no nível da imagem: a central de segurança não está verificando o registro, ele está verificando as imagens armazenadas no registro. 

Sempre que uma imagem é enviada para o registro, a central de segurança verifica a imagem automaticamente. Para disparar a verificação de uma imagem, envie-a por push para o repositório.


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
