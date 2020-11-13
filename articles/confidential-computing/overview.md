---
title: Visão geral da computação confidencial do Azure
description: Visão geral da Azure Confidential (ACC) Computação
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: overview
ms.date: 09/22/2020
ms.author: JenCook
ms.openlocfilehash: 44479db6b29075e87b0d2dcef6f0d9bc1881738c
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94560830"
---
# <a name="confidential-computing-on-azure"></a>Computação confidencial em Azure

A computação confidencial Azure permite isolar os seus dados sensíveis enquanto está a ser processado na nuvem. Muitas indústrias utilizam a computação confidencial para proteger os seus dados utilizando a computação confidencial para:

- Dados financeiros seguros
- Proteger a informação do paciente
- Executar processos de aprendizagem automática em informações sensíveis
- Executar algoritmos em conjuntos de dados encriptados de várias fontes


## <a name="overview"></a>Descrição geral
<p><p>


> [!VIDEO https://www.youtube.com/embed/rT6zMOoLEqI]

Sabemos que proteger os seus dados em nuvem é importante. Ouvimos as suas preocupações. Aqui estão apenas algumas perguntas que os nossos clientes podem ter ao mover cargas de trabalho sensíveis para a nuvem: 

- Como posso garantir que a Microsoft não pode aceder a dados que não estão encriptados?
- Como posso evitar ameaças de segurança de administradores privilegiados dentro da minha empresa?
- Quais são as formas mais que posso impedir terceiros de aceder a dados confidenciais do cliente?

O Microsoft Azure ajuda-o a minimizar a superfície de ataque para obter uma proteção de dados mais forte. O Azure já oferece muitas ferramentas para salvaguardar [**os dados em repouso**](../security/fundamentals/encryption-atrest.md) através de modelos como encriptação do lado do cliente e encriptação do lado do servidor. Além disso, o Azure oferece mecanismos para encriptar [**dados em trânsito**](../security/fundamentals/data-encryption-best-practices.md#protect-data-in-transit) através de protocolos seguros como TLS e HTTPS. Esta página introduz uma terceira perna de encriptação de dados - a encriptação de **dados em uso**.

## <a name="introduction-to-confidential-computing"></a>Introdução à computação confidencial 

A computação confidencial é um termo da indústria definido pelo [Consórcio De Computação Confidencial](https://confidentialcomputing.io/) (CCC) - uma fundação dedicada a definir e acelerar a adoção de computação confidencial. O CCC define a computação confidencial como: A proteção dos dados em uso através da realização de computações num Ambiente de Execução Fidedigna (TEE) baseado em hardware.

Um TEE é um ambiente que impõe a execução de apenas código autorizado. Quaisquer dados no TEE não podem ser lidos ou adulterados por qualquer código fora desse ambiente. 

### <a name="lessen-the-need-for-trust"></a>Diminuir a necessidade de confiança
Correr cargas de trabalho na nuvem requer confiança. Você dá esta confiança a vários fornecedores que permitem diferentes componentes da sua aplicação.


**Fornecedores de software de aplicações** : Confie no software implementando on-prem, utilizando código aberto ou construindo software de aplicação interna.

**Fornecedores de hardware** : Confie no hardware utilizando hardware no local ou hardware interno. 

**Fornecedores de infraestruturas** : Confie em fornecedores de nuvem ou gere os seus próprios centros de dados no local.


A computação confidencial Azure facilita a confiança no fornecedor de nuvem, reduzindo a necessidade de confiança em vários aspetos da infraestrutura de nuvem computacional. A computação confidencial Azure minimiza a confiança para o núcleo de OS do hospedeiro, o hipervisor, o administrador VM e o administrador anfitrião.

### <a name="reducing-the-attack-surface"></a>Reduzindo a superfície de ataque
A base de computação fidedigna (TCB) refere-se a todos os componentes de hardware, firmware e software de um sistema que proporcionam um ambiente seguro. Os componentes dentro do TCB são considerados "críticos". Se um componente dentro do TCB estiver comprometido, toda a segurança do sistema pode estar comprometida. 

Um TCB mais baixo significa maior segurança. Há menos risco de exposição a várias vulnerabilidades, malware, ataques e pessoas maliciosas. A azure computação confidencial tem como objetivo baixar o TCB para as suas cargas de trabalho em nuvem, oferecendo TEEs. Os TEEs reduzem o seu TCB a binários de tempo de execução, código e bibliotecas confiáveis. Quando utilizar infraestruturas e serviços Azure para computação confidencial, pode remover toda a Microsoft do seu TCB.


## <a name="using-azure-for-cloud-based-confidential-computing"></a>Utilização do Azure para computação confidencial baseada em nuvem <a id="cc-on-azure"></a>

A computação confidencial Azure permite-lhe alavancar capacidades de computação confidenciais num ambiente virtualizado. Agora pode utilizar ferramentas, software e infraestruturas em nuvem para construir em cima de hardware seguro.  

**Impedir o acesso não autorizado** : Executar dados sensíveis na nuvem. Confie que a Azure fornece a melhor proteção de dados possível, com pouca ou nenhuma alteração do que é feito hoje.

**Conformidade regulamentar** : Migrar para a nuvem e manter o controlo total dos dados para satisfazer os regulamentos governamentais para proteger informações pessoais e garantir o IP organizacional.

**Colaboração segura e não fidedtiva** : Enfrente problemas à escala de trabalho em toda a indústria, penteando dados entre organizações, mesmo concorrentes, para desbloquear uma ampla análise de dados e insights mais profundos.

**Processamento isolado** : Ofereça uma nova onda de produtos que removam a responsabilidade sobre dados privados com processamento cego. Os dados do utilizador nem sequer podem ser recuperados pelo prestador de serviços. 

## <a name="get-started"></a>Começar Agora
### <a name="azure-compute"></a>Computação do Azure
Construa aplicações em cima de ofertas confidenciais do IaaS computacional em Azure.
- Máquinas Virtuais (VMs): [Série DCsv2](confidential-computing-enclaves.md)
- Azure Kubernetes (AKS): [Orquestrar recipientes confidenciais](confidential-nodes-aks-overview.md)

### <a name="azure-security"></a>Azure Security 
Certifique-se de que as suas cargas de trabalho estão seguras através de métodos de verificação e gestão de chaves ligadas a hardware. 
- Atestado: [Microsoft Azure Atesstation (Preview)](../attestation/overview.md)
- Gestão chave: Managed-HSM (Pré-visualização)

### <a name="develop"></a>Programar
Comece a usar aplicações conscientes do enclave e implemente algoritmos confidenciais usando o quadro de inferenculação confidencial.
- Escrever aplicações para executar em DCsv2 VMs: [Open-enclave SDK](https://github.com/openenclave/openenclave)
- Modelos ML confidenciais em tempo de execução ONNX: [Inferencing confidencial (beta)](https://aka.ms/confidentialinference)

## <a name="next-steps"></a>Passos seguintes

Instale uma máquina virtual DCsv2-Series e instale o OE SDK nela.

> [!div class="nextstepaction"]
> [Implementar um VM de computação confidencial no Azure Marketplace](quick-create-marketplace.md)