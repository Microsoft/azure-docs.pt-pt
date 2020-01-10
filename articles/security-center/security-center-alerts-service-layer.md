---
title: Detecção de ameaças da camada de serviço do Azure – central de segurança do Azure
description: Este tópico apresenta os alertas da camada de serviço do Azure disponíveis na central de segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 08/25/2019
ms.author: memildin
ms.openlocfilehash: d4033989830323856ac14ed06eea7df74806f128
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665699"
---
# <a name="threat-detection-for-the-azure-service-layer-in-azure-security-center"></a>Detecção de ameaças para a camada de serviço do Azure na central de segurança do Azure

Este tópico apresenta os alertas da central de segurança do Azure disponíveis ao monitorar as seguintes camadas de serviço do Azure:

* [Camada de rede do Azure](#network-layer)
* [Camada de gerenciamento do Azure (Azure Resource Manager) (visualização)](#management-layer)
* [Cofre de Chaves do Azure](#azure-keyvault)

## Camada de rede do Azure<a name="network-layer"></a>

A central de segurança rede – a análise de camada baseia-se nos [dados IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)de exemplo, que são os cabeçalhos de pacote coletados pelos roteadores do Azure Core. Com base nesse feed de dados, a central de segurança usa modelos de aprendizado de máquina para identificar e sinalizar atividades de tráfego mal-intencionado. A central de segurança também usa o banco de dados de inteligência contra ameaças da Microsoft para enriquecer os endereços IP.

Algumas configurações de rede podem impedir que a central de segurança Gere alertas sobre a atividade de rede suspeita. Para a central de segurança gerar alertas de rede, verifique se:

- Sua máquina virtual tem um endereço IP público (ou está em um balanceador de carga com um endereço IP público).

- O tráfego de saída da rede da sua máquina virtual não está bloqueado por uma solução de IDS externa.

- Sua máquina virtual recebeu o mesmo endereço IP para a hora inteira durante a qual a comunicação suspeita ocorreu. Isso também se aplica a VMs criadas como parte de um serviço gerenciado (por exemplo, AKS, databricks).

Para obter uma lista dos alertas da camada de rede do Azure, consulte a [tabela de referência de alertas](alerts-reference.md#alerts-azurenetlayer).

Para obter detalhes de como a central de segurança pode usar sinais relacionados à rede para aplicar a proteção contra ameaças, consulte [detecção heurística de DNS na central de segurança](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).


## Camada de gerenciamento do Azure (Azure Resource Manager) (visualização)<a name ="management-layer"></a>

A camada de proteção da central de segurança baseada no Azure Resource Manager está atualmente em versão prévia.

A central de segurança oferece uma camada adicional de proteção usando eventos Azure Resource Manager, que é considerada o plano de controle do Azure. Analisando os registros de Azure Resource Manager, a central de segurança detecta operações comuns ou potencialmente prejudiciais no ambiente de assinatura do Azure.

Para obter uma lista dos alertas de Azure Resource Manager (versão prévia), consulte a [tabela de referência de alertas](alerts-reference.md#alerts-azureresourceman).



>[!NOTE]
> Várias das análises anteriores são alimentadas por Microsoft Cloud App Security. Para se beneficiar dessas análises, você deve ativar uma licença de Cloud App Security. Se você tiver uma licença Cloud App Security, esses alertas serão habilitados por padrão. Para desabilitá-los:
>
> 1. Na folha **central de segurança** , selecione **política de segurança**. Para a assinatura que você deseja alterar, selecione **Editar configurações**.
> 2. Selecione **detecção de ameaças**.
> 3. Em **habilitar integrações**, desmarque **permitir que Microsoft Cloud app Security acesse meus dados**e selecione **salvar**.

>[!NOTE]
>A central de segurança armazena dados de clientes relacionados à segurança na mesma área geográfica de seu recurso. Se a Microsoft ainda não tiver implantado a central de segurança na área geográfica do recurso, ele armazenará os dados no Estados Unidos. Quando Cloud App Security está habilitado, essas informações são armazenadas de acordo com as regras de localização geográfica do Cloud App Security. Para obter mais informações, consulte [armazenamento de dados para serviços não regionais](https://azuredatacentermap.azurewebsites.net/).

## Azure Key Vault (versão prévia)<a name="azure-keyvault"></a>

Azure Key Vault é um serviço de nuvem que protege chaves de criptografia e segredos como certificados, cadeias de conexão e senhas. 

A central de segurança do Azure inclui proteção de ameaças avançada e nativa do Azure para Azure Key Vault, fornecendo uma camada adicional de inteligência de segurança. A central de segurança detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de Key Vault. Essa camada de proteção permite que você resolva as ameaças sem ser um especialista em segurança e sem a necessidade de gerenciar sistemas de monitoramento de segurança de terceiros.  

Quando ocorrem atividades anômalas, a central de segurança mostra alertas e, opcionalmente, os envia por email aos administradores de assinatura. Esses alertas incluem os detalhes da atividade suspeita e recomendações sobre como investigar e corrigir ameaças. 

> [!NOTE]
> Esse serviço não está disponível no momento nas regiões do Azure governamental e do soberanas Cloud.

Para obter uma lista dos alertas de Azure Key Vault, consulte a [tabela de referência de alertas](alerts-reference.md#alerts-azurekv).
