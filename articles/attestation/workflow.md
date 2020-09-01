---
title: Fluxo de trabalho Azure Attestation
description: O fluxo de trabalho de Azure Attestation.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 240b27f897d8e7a34026701cf7fdc844eb9d4086
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89237381"
---
# <a name="workflow"></a>Fluxo de trabalho

O Microsoft Azure Attestation recebe provas da enclavess e avalia as provas contra a linha de base de segurança Azure e políticas configuráveis. Após a verficiação bem sucedida, a Azure Attestation gera um token de atestado para confirmar a fiabilidade do enclave.

Os seguintes intervenientes estão envolvidos num fluxo de trabalho da Azure Attestation:

- **Partido de base**: O componente que conta com o Azure Attestation para verificar a validade do enclave. 
- **Cliente**: O componente que recolhe informações de um enclave e envia pedidos para a Azure Attestation. 
- **Azure Attestation**: O componente que aceita provas do enclave do cliente, valida-a e devolve ficha de atestado ao cliente


## <a name="enclave-validation-work-flow"></a>Fluxo de trabalho de validação do enclave

Aqui estão os passos gerais num fluxo de trabalho típico do enclave SGX (usando a azure Attestation):

1. O cliente recolhe provas de um enclave. As provas são informações sobre o ambiente do enclave e a biblioteca de clientes que funciona dentro do enclave.
1. O cliente tem um URI que se refere a um caso de Azure Attestation. O cliente autentica-se na Azure AD e obtém um token de acesso.
1. O cliente envia provas para a Azure Attestation juntamente com o token de acesso. As informações exatas apresentadas ao fornecedor dependem do tipo enclave.
1. O Azure Attestation valida a informação submetida e avalia-a contra uma política configurada. Se a verificação for bem sucedida, a Azure Attestation emite um token de atestado e devolve-a ao cliente. Se este passo falhar, a Azure Attestation reporta um erro ao cliente. 
1. O cliente envia o token do atestado para a festa de confia. O grupo invocando chama o ponto final de metadados públicos do Azure Attestation para recuperar certificados de assinatura. O grupo de confiança verifica então a assinatura do token do atestado e assegura a fiabilidade do enclave. 

![Fluxo de validação do enclave](./media/validation-flow.png)


## <a name="next-steps"></a>Passos seguintes
- [Como autor e assinar uma política de atestado](author-sign-policy.md)
- [Configurar a Azure Attestation usando o PowerShell](quickstart-powershell.md)
