---
title: Visão geral da computação confidencial do Azure
description: Visão geral da Azure Confidential (ACC) Computação
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: overview
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: 4e92f974ce7d6c03143276808c4ca4d09d607a84
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87835821"
---
# <a name="confidential-computing-on-azure"></a>Computação confidencial em Azure

A computação confidencial Azure permite isolar os seus dados sensíveis enquanto está a ser processado na nuvem. Muitas indústrias usam computação confidencial para proteger os seus dados. Estas cargas de trabalho incluem:

- Assegurar dados financeiros
- Proteger a informação do paciente
- Executando processos de aprendizagem automática em informações sensíveis
- Executando algoritmos em conjuntos de dados encriptados de várias fontes


## <a name="overview"></a>Descrição geral
<p><p>


> [!VIDEO https://www.youtube.com/embed/rT6zMOoLEqI]

Sabemos que proteger os seus dados em nuvem é importante. Ouvimos as suas preocupações. Aqui estão apenas algumas perguntas que os nossos clientes podem ter ao mover cargas de trabalho sensíveis para a nuvem: 

- Como posso garantir que a Microsoft não pode aceder a dados que não estão encriptados?
- Como posso evitar ameaças de segurança de administradores privilegiados dentro da minha empresa?
- Quais são as formas mais que posso impedir terceiros de aceder a dados confidenciais do cliente?

O Microsoft Azure ajuda-o a minimizar a superfície de ataque para obter uma proteção de dados mais forte. O Azure já oferece muitas ferramentas para salvaguardar [**os dados em repouso**](../security/fundamentals/encryption-atrest.md) através de modelos como encriptação do lado do cliente e encriptação do lado do servidor. Além disso, o Azure oferece mecanismos para encriptar [**dados em trânsito**](../security/fundamentals/data-encryption-best-practices.md#protect-data-in-transit) através de protocolos seguros como TLS e HTTPS. Esta página introduz uma terceira perna de encriptação de dados - a encriptação de **dados em uso**.


## <a name="introduction-to-confidential-computing"></a>Introdução à computação confidencial<a id="intro to acc"></a>

A computação confidencial é um termo da indústria definido pelo [Consórcio De Computação Confidencial](https://confidentialcomputing.io/) (CCC) - uma fundação dedicada a definir e acelerar a adoção de computação confidencial. O CCC define a computação confidencial como a proteção dos dados em uso através da realização de computações num Ambiente de Execução Fidedigna (TEE) baseado em hardware.

Um TEE é um ambiente que impõe a execução de apenas código autorizado. Quaisquer dados no TEE não podem ser lidos ou adulterados por qualquer código fora desse ambiente.

### <a name="enclaves"></a>Enclaves

Os enclaves são partes seguras do processador e memória de um hardware. Não há como ver dados ou código dentro do enclave, mesmo com um depuração. Se as tentativas de código não fidedidas modificarem o conteúdo na memória do enclave, o ambiente fica desativado e as operações são negadas.

Ao desenvolver aplicações, pode utilizar [ferramentas](#oe-sdk) de software para proteger partes do seu código e dados dentro do enclave. Estas ferramentas garantirão que o seu código e dados não podem ser vistos ou modificados por ninguém fora do ambiente de confiança. 

Fundamentalmente, pense num enclave como uma caixa segura. Coloca-se código e dados encriptados na caixa. Do lado de fora da caixa, não se vê nada. Você dá ao enclave uma chave para desencriptar os dados, os dados são então processados e encriptados novamente, antes de serem enviados para fora do enclave.

### <a name="attestation"></a>Atestado

Vai querer obter a verificação e validação de que o seu ambiente de confiança é seguro. Esta verificação é o processo de atestado. 

O Attestation permite que uma parte dependente tenha uma maior confiança de que o seu software está (1) a funcionar num enclave e (2) que o enclave está atualizado e seguro. Por exemplo, um enclave pede ao hardware subjacente para gerar uma credencial que inclua a prova de que o enclave existe na plataforma. O relatório pode então ser entregue a um segundo enclave que verifica que o relatório foi gerado na mesma plataforma.

O atestado deve ser implementado utilizando um serviço de atestado seguro que seja compatível com o software do sistema e o silício. [Os serviços de atestado e provisionamento da Intel](https://software.intel.com/sgx/attestation-services) são compatíveis com máquinas virtuais de computação confidencial da Azure.


## <a name="using-azure-for-cloud-based-confidential-computing"></a>Utilização do Azure para computação confidencial baseada em nuvem<a id="cc-on-azure"></a>

A computação confidencial Azure permite-lhe alavancar capacidades de computação confidenciais num ambiente virtualizado. Agora pode utilizar ferramentas, software e infraestruturas em nuvem para construir em cima de hardware seguro. 

### <a name="virtual-machines"></a>Máquinas Virtuais

O Azure é o primeiro fornecedor de nuvem a oferecer computação confidencial num ambiente virtualizado. Desenvolvemos máquinas virtuais que funcionam como uma camada de abstração entre o hardware e a sua aplicação. Pode executar cargas de trabalho em escala e com opções de redundância e disponibilidade.  

#### <a name="intel-sgx-enabled-virtual-machines"></a>Máquinas Virtuais ativadas pela Intel SGX

Em máquinas virtuais de computação confidencial Azure, uma parte do hardware do CPU é reservada para uma parte do código e dados na sua aplicação. Esta parte restrita é o enclave. 

![Modelo VM](media/overview/hardware-backed-enclave.png)

A infraestrutura de computação confidencial Azure é atualmente composta por uma especialidade SKU de máquinas virtuais (VMs). Estes VMs funcionam em processadores Intel com extensão de guarda de software (Intel SGX). [O Intel SGX](https://intel.com/sgx) é o componente que permite uma maior proteção que iluminamos com computação confidencial. 

Hoje, a Azure oferece a [Série DCsv2](https://docs.microsoft.com/azure/virtual-machines/dcv2-series) construída sobre a tecnologia Intel SGX para criação de enclave baseado em hardware. Pode construir aplicações seguras baseadas em enclaves para executar na série de VMs DCsv2 para proteger os dados da sua aplicação e código em uso. 

Você pode [ler mais](virtual-machine-solutions.md) sobre a implementação de máquinas virtuais confidenciais de computação Azure com enclaves de confiança baseados em hardware.

## <a name="application-development"></a>Desenvolvimento de aplicações<a id="application-development"></a>

Para aproveitar o poder dos enclaves e ambientes isolados, você precisará usar ferramentas que suportem a computação confidencial. Existem várias ferramentas que apoiam o desenvolvimento de aplicações do enclave. Por exemplo, pode utilizar estes quadros de código aberto: 

- [O Kit de Desenvolvimento de Software Open Enclave (SDK)](https://github.com/openenclave/openenclave)
- [O Quadro Confidencial do Consórcio (CCF)](https://github.com/Microsoft/CCF)

### <a name="overview"></a>Descrição geral

Uma aplicação construída com enclaves é dividida de duas maneiras:
1. Um componente "não-fidedquirso" (o hospedeiro)
1. Um componente "confiável" (o enclave)

**O hospedeiro** é onde a sua aplicação do enclave está em cima e é um ambiente não-fidedquirso. O código do enclave implantado no hospedeiro não pode ser acedido pelo hospedeiro. 

**O enclave** é onde o código de aplicação e os seus dados/memória em cache são executados. Devem ocorrer computações seguras nos enclaves para garantir segredos e dados sensíveis, permanecer protegidos. 

Durante o design da aplicação, é importante identificar e determinar que parte da aplicação precisa de ser executada nos enclaves. O código que escolhe colocar no componente de confiança está isolado do resto da sua aplicação. Uma vez que o enclave é inicializado e o código é carregado para a memória, esse código não pode ser lido ou alterado a partir dos componentes não fidedidos. 

### <a name="open-enclave-software-development-kit-oe-sdk"></a>Kit aberto de desenvolvimento de software enclave (OE SDK)<a id="oe-sdk"></a>

Utilize uma biblioteca ou estrutura suportada pelo seu fornecedor se quiser escrever código que funciona num enclave. O [Open Enclave SDK](https://github.com/openenclave/openenclave) (OE SDK) é um SDK de código aberto que permite a abstração em diferentes hardware confidenciais computação. 

O OE SDK é construído para ser uma única camada de abstração sobre qualquer hardware em qualquer CSP. O OE SDK pode ser usado em cima de máquinas virtuais de computação confidencial Azure para criar e executar aplicações em cima de enclaves.

## <a name="next-steps"></a>Passos seguintes

Instale uma máquina virtual série DCsv2 e instale o OE SDK nela.

> [!div class="nextstepaction"]
> [Implementar um VM de Computação Confidencial no Mercado Azure](quick-create-marketplace.md)
