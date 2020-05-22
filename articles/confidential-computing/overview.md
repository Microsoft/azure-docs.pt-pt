---
title: Visão geral da computação confidencial azure
description: Visão geral da Computação Azure Confidential (ACC)
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: overview
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: 44006bdfd9ffe6e78380adefe9271f42c0a76f84
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773284"
---
# <a name="confidential-computing-on-azure"></a>Computação confidencial em Azure

A computação confidencial Azure permite isolar os seus dados sensíveis enquanto está a ser processado na nuvem. Muitas indústrias usam computação confidencial para proteger os seus dados. Estas cargas de trabalho incluem:

- Obtenção de dados financeiros
- Proteger a informação do paciente
- Processos de aprendizagem automática em informação sensível
- Executar algoritmos em conjuntos de dados encriptados de várias fontes


## <a name="overview"></a>Descrição geral
<p><p>


> [!VIDEO https://www.youtube.com/embed/rT6zMOoLEqI]

Sabemos que proteger os seus dados em nuvem é importante. Ouvimos as suas preocupações. Aqui estão apenas algumas perguntas que os nossos clientes podem ter ao mover cargas de trabalho sensíveis para a nuvem: 

- Como posso garantir que a Microsoft não pode aceder a dados que não estão encriptados?
- Como posso evitar ameaças à segurança de administradores privilegiados dentro da minha empresa?
- Quais são mais formas de impedir terceiros de acederem a dados sensíveis dos clientes?

O Microsoft Azure ajuda-o a minimizar a sua superfície de ataque para obter uma proteção de dados mais forte. O Azure já oferece muitas ferramentas para salvaguardar [**dados em repouso**](../security/fundamentals/encryption-atrest.md) através de modelos como encriptação do lado do cliente e encriptação do lado do servidor. Além disso, o Azure oferece mecanismos para encriptar [**dados em trânsito**](../security/fundamentals/data-encryption-best-practices.md#protect-data-in-transit) através de protocolos seguros como TLS e HTTPS. Esta página introduz uma terceira perna de encriptação de dados - a encriptação de **dados em uso**.


## <a name="introduction-to-confidential-computing"></a>Introdução à computação confidencial<a id="intro to acc"></a>

A computação confidencial é um termo da indústria definido pelo [Consórcio de Computação Confidencial](https://confidentialcomputing.io/) (CCC) - uma fundação dedicada a definir e acelerar a adoção de computação confidencial. O CCC define a computação confidencial como a proteção de dados em uso através da realização de cálculos num Ambiente de Execução Fidedigna baseado em hardware (TEE).

Um TEE é um ambiente que impõe a execução de apenas código autorizado. Quaisquer dados no TEE não podem ser lidos ou adulterados por qualquer código fora desse ambiente.

### <a name="enclaves"></a>Enclaves

Os enclaves são porções seguras do processador e da memória de um hardware. Não há como ver dados ou código dentro do enclave, mesmo com um desordeiro. Se as tentativas de código não fidedignos modificarem o conteúdo na memória do enclave, o ambiente fica desativado e as operações são negadas.

Ao desenvolver aplicações, pode utilizar [ferramentas](#oe-sdk) de software para proteger partes do seu código e dados dentro do enclave. Estas ferramentas garantirão que o seu código e dados não podem ser vistos ou modificados por ninguém fora do ambiente de confiança. 

Fundamentalmente, pense num enclave como uma caixa preta. Coloca código e dados encriptados na caixa. Do lado de fora da caixa, não se vê nada. Você dá ao enclave uma chave para desencriptar os dados, os dados são então processados e encriptados novamente, antes de serem enviados para fora do enclave.

### <a name="attestation"></a>Atestação

Você vai querer obter verificação e validação de que o seu ambiente de confiança é seguro. Esta verificação é o processo de atestado. 

A Attestation permite que uma parte que depende tenha uma maior confiança de que o seu software está (1) a funcionar num enclave e (2) que o enclave está atualizado e seguro. Por exemplo, um enclave pede ao hardware subjacente para gerar uma credencial que inclui a prova de que o enclave existe na plataforma. O relatório pode então ser entregue a um segundo enclave que verifica que o relatório foi gerado na mesma plataforma.

A atestadodeve ser implementada utilizando um serviço de atestado seguro compatível com o software e o silício do sistema. Os [serviços de atestado e provisionamento da Intel](https://software.intel.com/sgx/attestation-services) são compatíveis com máquinas virtuais de computação confidencial Azure.


## <a name="using-azure-for-cloud-based-confidential-computing"></a>Utilização do Azure para computação confidencial baseada na nuvem<a id="cc-on-azure"></a>

A computação confidencial Azure permite-lhe alavancar capacidades de computação confidenciais num ambiente virtualizado. Agora pode usar ferramentas, software e infraestrutura seleto para construir em cima de hardware seguro. 

### <a name="virtual-machines"></a>Virtual Machines

O Azure é o primeiro fornecedor de nuvem a oferecer computação confidencial num ambiente virtualizado. Desenvolvemos máquinas virtuais que funcionam como uma camada de abstração entre o hardware e a sua aplicação. Você pode executar cargas de trabalho em escala e com opções de redundância e disponibilidade.  

#### <a name="intel-sgx-enabled-virtual-machines"></a>Máquinas virtuais ativadas pela Intel SGX

Em máquinas virtuais de computação confidencial azure, uma parte do hardware do CPU está reservada para uma porção de código e dados na sua aplicação. Esta parte restrita é o enclave. 

![Modelo VM](media/overview/hardware-backed-enclave.png)

A infraestrutura de computação confidencial Azure é atualmente composta por uma especialidade SKU de máquinas virtuais (VMs). Estes VMs funcionam em processadores Intel com Extensão de Guarda de Software (Intel SGX). [Intel SGX](https://intel.com/sgx) é o componente que permite o aumento da proteção que iluminamos com computação confidencial. 

Hoje, o Azure oferece o [DCsv2-Series](https://docs.microsoft.com/azure/virtual-machines/dcv2-series) construído sobre a tecnologia Intel SGX para criação de enclave baseado em hardware. Pode construir aplicações seguras baseadas em enclaves para executar na série DCsv2 de VMs para proteger os dados da sua aplicação e código em uso. 

Pode [ler mais](virtual-machine-solutions.md) sobre a implementação de máquinas virtuais de computação confidencial azure com enclaves fidedignos baseados em hardware.

## <a name="application-development"></a>Desenvolvimento de aplicações<a id="application-development"></a>

Para aproveitar o poder dos enclaves e ambientes isolados, terá de usar ferramentas que apoiem a computação confidencial. Existem várias ferramentas que apoiam o desenvolvimento de aplicações do enclave. Por exemplo, pode utilizar estes quadros de código aberto: 

- [O Kit de Desenvolvimento de Software do Enclave Aberto (SDK)](https://github.com/openenclave/openenclave)
- [Quadro confidencial do consórcio (CCF)](https://github.com/Microsoft/CCF)

### <a name="overview"></a>Descrição geral

Uma aplicação construída com enclaves é dividida de duas formas:
1. Um componente "não confiável" (o hospedeiro)
1. Um componente "confiável" (o enclave)

**O hospedeiro** é onde a sua aplicação do enclave está em execução e é um ambiente não confiável. O código do enclave implantado no hospedeiro não pode ser acedido pelo hospedeiro. 

**O enclave** é onde corre o código de aplicação e os seus dados/memória em cache. Computações seguras devem ocorrer nos enclaves para garantir segredos e dados sensíveis, permanecer protegidos. 

Durante o projeto da aplicação, é importante identificar e determinar que parte da aplicação precisa para ser executada nos enclaves. O código que escolhe colocar no componente fidedigno está isolado do resto da sua aplicação. Uma vez que o enclave é inicializado e o código é carregado para a memória, esse código não pode ser lido ou alterado dos componentes não confiáveis. 

### <a name="open-enclave-software-development-kit-oe-sdk"></a>Kit de desenvolvimento de software de enclave aberto (OE SDK)<a id="oe-sdk"></a>

Utilize uma biblioteca ou enquadramento suportado pelo seu fornecedor se quiser escrever um código que funciona num enclave. O [Open Enclave SDK](https://github.com/openenclave/openenclave) (OE SDK) é um SDK de código aberto que permite a abstração sobre diferentes hardware confidenciais habilitados para computação. 

O OE SDK foi construído para ser uma única camada de abstração sobre qualquer hardware em qualquer CSP. O OE SDK pode ser usado em cima de máquinas virtuais de computação confidencial Azure para criar e executar aplicações em cima de enclaves.

## <a name="next-steps"></a>Passos seguintes

Implante uma máquina virtual dCsv2-Series e instale o OE SDK nele.

> [!div class="nextstepaction"]
> [Implementar um VM de Computação Confidencial no Mercado Azure](quick-create-marketplace.md)