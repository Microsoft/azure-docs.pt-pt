---
title: Visão geral da azure Attestation
description: Uma visão geral do Microsoft Azure Attestation, uma solução para atestar Ambientes de Execução Fidedignos (TEEs)
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: references_regions
ms.openlocfilehash: a84308ba06a38cea475fcb1bae022da16424a731
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "90033001"
---
# <a name="microsoft-azure-attestation-preview"></a>Microsoft Azure Attestation (pré-visualização)

O Microsoft Azure Attestation (pré-visualização) é uma solução para atestar ambientes de execução fidedignos (TEEs) tais como [enclaves de extensões](https://www.intel.com/content/www/us/en/architecture-and-technology/software-guard-extensions.html) de redes de software intel® (SGX) e enclaves [de segurança baseadas em virtualização](/windows-hardware/design/device-experiences/oem-vbs) (VBS). O atestado enclave é um processo para verificar se um enclave é seguro e de confiança.

Attestation é um processo para demonstrar que binários de software foram corretamente instantâneos numa plataforma de confiança. As partes de confiança remota podem então ganhar confiança de que apenas esse software pretendido está a ser gerido em hardware de confiança. O Azure Attestation é um serviço unificado virado para o cliente e enquadramento para a atestada.

O Azure Attestation permite paradigmas de segurança de ponta, como [a computação Azure Confidential](../confidential-computing/overview.md) e a proteção intelligent Edge. Os clientes têm vindo a solicitar a possibilidade de verificar de forma independente a localização de uma máquina, a postura de uma máquina virtual (VM) naquela máquina, e o ambiente em que os enclaves estão a funcionar nesse VM. A Azure Attestation capacitará estes e muitos pedidos adicionais de clientes.

O Azure Attestation recebe provas de entidades computadoras, transforma-as num conjunto de reclamações, valida-as contra políticas configuráveis e produz provas criptográficas para aplicações baseadas em sinistros (por exemplo, partes dependentes e autoridades de auditoria).

## <a name="use-cases"></a>Casos de utilização

A Azure Attestation oferece serviços abrangentes de atestado para vários ambientes e casos de uso distintos.

### <a name="sgx-attestation"></a>Atestado SGX

SGX refere-se ao isolamento de grau de hardware, que é suportado em certos modelos de CPUs da Intel. A SGX permite que o código seja executado em compartimentos higienizados conhecidos como enclaves SGX. As permissões de acesso e memória são então geridas por hardware para garantir uma superfície de ataque mínima com isolamento adequado.

As aplicações do cliente podem ser projetadas para tirar partido dos enclaves da SGX, delegando tarefas sensíveis à segurança a serem feitas dentro desses enclaves. Tais aplicações podem então recorrer ao Azure Attestation para estabelecer rotineiramente confiança no enclave e sua capacidade de aceder a dados sensíveis.

### <a name="vbs-attestation"></a>Atestado VBS

VBS é uma arquitetura baseada em software para uma proteção de memória do enclave baseada em Hiper-V. Impede que o código de administração do hospedeiro, bem como os administradores de serviços locais e de nuvem, acedam aos dados num enclave VBS ou afetem a sua execução.

Semelhante à tecnologia SGX, a Azure Attestation suporta a validação dos enclaves VBS contra políticas configuradas e a emissão de uma declaração de certificação como prova de validade.

### <a name="open-enclave"></a>Enclave Aberto
[Open Enclave](https://openenclave.io/sdk/) (OE) é uma coleção de bibliotecas destinadas a criar uma única abstração unificada de escamação para os desenvolvedores para construir aplicações baseadas em TEE. Oferece um modelo de aplicação universal e seguro que minimiza as especificidades da plataforma. A Microsoft vê-o como um passo essencial para democratizar tecnologias de enclave baseadas em hardware, como a SGX e aumentar a sua absorção no Azure.

A OE normaliza requisitos específicos para a verificação de uma prova de enclave. Isto qualifica a OE como um consumidor de atestado altamente adequado da Azure Attestation.

## <a name="azure-attestation-can-run-in-a-tee"></a>Azure Attestation pode correr em um TEE

O Azure Attestation é fundamental para os cenários de Computação Confidencial, uma vez que executa as seguintes ações:

- Verifica se as provas do enclave são válidas.
- Avalia as provas do enclave contra uma política definida pelo cliente.
- Gere e armazena políticas específicas dos inquilinos.
- Gera e assina um símbolo que é usado por partes dependentes para interagir com o enclave.

A azure Attestation é construído para funcionar em dois tipos de ambientes:
- Azure Attestation a funcionar num TEE ativado pela SGX.
- Azure Attestation correndo num não-TEE.

Os clientes da Azure Attestation expressaram a exigência de que a Microsoft esteja operacionalmente fora da base de computação fidedigna (TCB). Isto é para evitar que entidades da Microsoft, como administradores VM, administradores de anfitriões e desenvolvedores da Microsoft, modifiquem pedidos de atestado, políticas e fichas emitidas pela Azure Attestation. A azure Attestation também foi construído para funcionar em TEE, onde características de Azure Attestation como validação de citações, geração de token e assinatura de token são transferidas para um enclave SGX.

## <a name="why-use-azure-attestation"></a>Por que usar a Azure Attestation

A azure Attestation é a escolha preferida para atestar tees, uma vez que oferece os seguintes benefícios: 

- Quadro unificado para atestar múltiplos TEEs, tais como enclaves SGX e enclaves VBS
- Serviço multi-inquilino que permite configurar fornecedores e políticas de atestado personalizados para restringir a geração de token
- Oferece fornecedores predefinidos que podem atestar sem configuração dos utilizadores
- Protege os seus dados durante a utilização com implementação num enclave SGX
- Serviço altamente disponível que oferecerá Contrato de Nível de Serviço (SLA)

## <a name="business-continuity-and-disaster-recovery-bcdr-support"></a>Apoio à continuidade do negócio e recuperação de desastres (BCDR)

[A Continuidade do Negócio e Recuperação de Desastres](/azure/best-practices-availability-paired-regions) (BCDR) para o Azure Attestation permite mitigar as perturbações do serviço resultantes de problemas de disponibilidade significativos ou eventos de desastres numa região.

Abaixo estão as regiões que são atualmente apoiadas pelo BCDR
- Leste DOS EUA 2 => Emparelhado com os EUA Centrais.
- Central US => Emparelhado com East US 2.

Os aglomerados implantados em duas regiões funcionarão de forma independente em circunstâncias normais. Em caso de avaria ou desatação de uma região, o seguinte se coloca:

- Azure Attestation BCDR fornecerá failover sem emenda em que os clientes não precisam de dar qualquer passo extra para recuperar
- O [Gestor de Tráfego da Azure](../traffic-manager/index.yml) para a região irá detetar que a sonda de saúde está degradada e mudar o ponto final para a região emparelhada
- As ligações existentes não funcionarão e receberão erros internos do servidor ou problemas de tempo limite
- Todas as operações do avião de controlo serão bloqueadas. Os clientes não serão capazes de criar fornecedores de atestados e atualizar políticas na região primária
- Todas as operações de avião de dados, incluindo chamadas atestais, continuarão a trabalhar na região primária

## <a name="next-steps"></a>Passos seguintes
- Conheça os [conceitos básicos do Azure Attestation](basic-concepts.md)
- [Como autor e assinar uma política de atestado](author-sign-policy.md)
- [Configurar a Azure Attestation usando o PowerShell](quickstart-powershell.md)

