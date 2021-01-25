---
title: Fluxo de trabalho Azure Attestation
description: O fluxo de trabalho de Azure Attestation.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 27a97ceb2ca9a7b58df7200930e4e47d89c9ae89
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98762196"
---
# <a name="workflow"></a>Fluxo de trabalho

O Microsoft Azure Attestation recebe provas de enclaves e avalia as provas contra a linha de base de segurança Azure e políticas configuráveis. Após uma verificação bem sucedida, a Azure Attestation gera um token de atestado para confirmar a fiabilidade do enclave.

Os seguintes intervenientes estão envolvidos num fluxo de trabalho da Azure Attestation:

- **Partido de base**: O componente que conta com o Azure Attestation para verificar a validade do enclave. 
- **Cliente**: O componente que recolhe informações de um enclave e envia pedidos para a Azure Attestation. 
- **Azure Attestation**: O componente que aceita provas do enclave do cliente, valida-a e devolve ficha de atestado ao cliente


## <a name="intel-software-guard-extensions-sgx-enclave-validation-work-flow"></a>Intel® Software Guard Extensions (SGX) fluxo de trabalho de validação do enclave

Aqui estão os passos gerais num fluxo de trabalho típico do enclave SGX (usando a azure Attestation):

1. O cliente recolhe provas de um enclave. As provas são informações sobre o ambiente do enclave e a biblioteca de clientes que funciona dentro do enclave.
1. O cliente tem um URI que se refere a um caso de Azure Attestation. O cliente envia provas para a Azure Attestation. As informações exatas apresentadas ao fornecedor dependem do tipo enclave.
1. O Azure Attestation valida a informação submetida e avalia-a contra uma política configurada. Se a verificação for bem sucedida, a Azure Attestation emite um token de atestado e devolve-a ao cliente. Se este passo falhar, a Azure Attestation reporta um erro ao cliente. 
1. O cliente envia o token do atestado para a festa de confia. O grupo invocando chama o ponto final de metadados públicos do Azure Attestation para recuperar certificados de assinatura. O grupo de confiança verifica então a assinatura do token do atestado e assegura a fiabilidade do enclave. 

![Fluxo de validação do enclave SGX](./media/sgx-validation-flow.png)

> [!Note]
> Quando envia pedidos de atestado na versão API [de pré-visualização 2018-09-01,](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/attestation/data-plane/Microsoft.Attestation/stable/2018-09-01-preview) o cliente precisa enviar provas para a Azure Attestation juntamente com o token de acesso Azure AD.

## <a name="trusted-platform-module-tpm-enclave-validation-work-flow"></a>Fluxo de trabalho de validação do módulo de plataforma fidedigno (TPM)

Aqui estão os passos gerais num fluxo de trabalho típico do atestado de atestado de enclave TPM (usando a atatão Azure):

1.  No arranque do dispositivo/plataforma, vários carregadores de arranque e serviços de arranque medem eventos que são apoiados pelo TPM e são armazenados de forma segura (log TCG).
2.  O cliente recolhe os registos TCG do dispositivo e da citação TPM, que atua como prova para atestado.
3.  O cliente tem um URI que se refere a um caso de Azure Attestation. O cliente envia provas para a Azure Attestation. A informação exata submetida ao fornecedor depende da plataforma.
4.  O Azure Attestation valida a informação submetida e avalia-a contra uma política configurada. Se a verificação for bem sucedida, a Azure Attestation emite um token de atestado e devolve-a ao cliente. Se este passo falhar, a Azure Attestation reporta um erro ao cliente. A comunicação entre o cliente e o serviço de atestado é ditada pelo protocolo Azure atesstation TPM.
5.  O cliente envia então o token do atestado para a festa de confia. O grupo invocando chama o ponto final de metadados públicos do Azure Attestation para recuperar certificados de assinatura. O grupo contando então verifica a assinatura do token atestado e garante a fiabilidade das plataformas.

![Fluxo de validação de TPM](./media/tpm-validation-flow.png)

## <a name="next-steps"></a>Próximos passos
- [Como autor e assinar uma política de atestado](author-sign-policy.md)
- [Configurar a Azure Attestation usando o PowerShell](quickstart-powershell.md)
