---
title: Cenários de computação confidencial do Azure comuns e casos de utilização
description: Entenda como usar a computação confidencial no seu cenário.
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: overview
ms.date: 9/22/2020
ms.author: jencook
ms.openlocfilehash: 47938f3a44c3a47f8b444b59d7e2f0867a274f33
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102566622"
---
# <a name="common-scenarios-for-azure-confidential-computing"></a>Cenários comuns para a computação confidencial do Azure

Este artigo fornece uma visão geral de vários cenários comuns para a computação confidencial Azure. As recomendações deste artigo servem como ponto de partida à medida que desenvolve a sua aplicação utilizando serviços e quadros de computação confidencial. 

Depois de ler este artigo, poderá responder às seguintes perguntas:

- Quais são alguns cenários para a computação confidencial do Azure?
- Quais são os benefícios de usar a computação confidencial Azure para cenários multi-partidários?
- Quais são os benefícios de usar a computação confidencial Azure numa rede blockchain?


## <a name="secure-multi-party-computation"></a>Computação multi-partidária segura
A computação confidencial Azure permite processar dados de várias fontes sem expor os dados de entrada a outras partes. Este tipo de computação segura permite muitos cenários como: anti-branqueamento de capitais, deteção de fraudes e análise segura dos dados dos cuidados de saúde.

Várias fontes podem enviar os seus dados para um enclave numa máquina virtual. Uma das partes diz ao enclave para fazer computação ou processamento dos dados. Nenhum partido (nem mesmo o que executa a análise) consegue ver os dados de outras partes que foram enviados para o enclave. 

Na computação multipartidária segura, os dados encriptados vão para o enclave, o enclave desencripta os dados usando uma chave, executa a análise, obtém um resultado, e envia de volta um resultado encriptado que uma parte pode desencriptar com a chave designada. 

**Proteger os dados em uso:** 
- Utilize uma máquina virtual DCsv2-Series (VM) no Azure com suporte Intel SGX ativado. Estes VMs estão habilitados com ambientes de execução fidedignos (TEEs) que asseguram e isolam partes dos dados e códigos da sua aplicação.
- Use um SDK consciente do enclave para criar um enclave dentro da máquina virtual. Dentro do enclave, os dados não serão expostos a nenhum, mesmo o fornecedor de VM. Os dados no enclave serão encriptados por suporte de hardware.
    - Por exemplo, pode utilizar o [OE SDK](https://github.com/openenclave/openenclave) para o processamento do lado do servidor. 

**Proteger os dados em trânsito** 
- Utilizar TLS atestado como um canal seguro para garantir a segurança dos dados em trânsito
- O cliente garante que os dados são enviados apenas para o mesmo servidor, que está protegido pelo enclave. 

**Proteger os dados inativos**
- Utilize lojas de dados protegidas e seguras para garantir a segurança dos dados durante o descanso 

### <a name="anti-money-laundering"></a>Combate ao branqueamento de capitais
Neste exemplo de computação multipartidária seguro, vários bancos partilham dados uns com os outros sem expor dados pessoais dos seus clientes. Os bancos executam análises acordadas no conjunto de dados sensíveis combinados. A análise do conjunto de dados agregado pode detetar o movimento de dinheiro por um utilizador entre vários bancos, sem que os bancos acedam aos dados uns dos outros.

Através da computação confidencial, estas instituições financeiras podem aumentar as taxas de deteção de fraudes, abordar cenários de branqueamento de capitais, reduzir falsos positivos e continuar a aprender com conjuntos de dados maiores. 

### <a name="drug-development-in-healthcare"></a>Desenvolvimento de fármacos nos cuidados de saúde
As instalações de saúde parceiras contribuem com conjuntos de dados de saúde privados para formar um modelo ML. Cada instalação só pode ver o seu próprio conjunto de dados. Nenhuma outra instalação ou mesmo o fornecedor de nuvem, pode ver os dados ou o modelo de treino. 

![Análise de saúde do paciente](./media/use-cases-scenarios/patient-data.png)

Todas as instalações beneficiam da utilização do modelo treinado. Ao criar o modelo com mais dados, o modelo tornou-se mais preciso. Cada instalação que contribuiu para a formação do modelo pode usá-lo e receber resultados úteis. 

## <a name="blockchain"></a>Blockchain

Uma rede blockchain é uma rede descentralizada de nós. Estes nós são executados e mantidos por operadores ou validadores que desejem garantir a integridade e chegar a um consenso sobre o estado da rede. Os nós em si são réplicas de livros e são usados para rastrear transações blockchain. Cada nó tem uma cópia completa do histórico de transações, garantindo integridade e disponibilidade numa rede distribuída.

As tecnologias blockchain construídas em cima da computação confidencial podem usar a privacidade baseada em hardware para permitir a confidencialidade dos dados e computaçãos seguras. Em alguns casos, todo o livro-razão é encriptado para salvaguardar o acesso aos dados. Às vezes, a transação em si pode ocorrer dentro de um módulo de computação dentro do enclave dentro do nó.

### <a name="confidential-consortium-framework-ccf"></a>Quadro confidencial do consórcio (CCF)
O [CCF](https://www.microsoft.com/research/project/confidential-consortium-framework/) é um exemplo de uma estrutura blockchain distribuída construída em cima da computação confidencial Azure. Liderado pela Microsoft Research, este quadro aproveita o poder de ambientes de execução fidedignos (TEEs) para criar uma rede de enclaves remotos para atestado. Os nós podem funcionar em cima de máquinas virtuais Azure[(Série DCsv2)](confidential-computing-enclaves.md)e tirar partido da infraestrutura do enclave. Através de protocolos de atestação, os utilizadores da blockchain podem verificar a integridade de um nó CCF e verificar eficazmente toda a rede. 

![Uma rede de nóns](./media/use-cases-scenarios/ccf.png)

No CCF, o livro-razão descentralizado é composto por alterações registadas numa loja Key-Value que é replicada em todos os nós da rede. Cada um destes nós tem um motor de transação que pode ser acionado pelos utilizadores da blockchain sobre tLS. Quando disparas um ponto final, mutas a loja Key-Value. Antes que a alteração encriptada seja gravada no livro de contabilidade descentralizado, deve ser acordado por um certo número de nós para chegar a um consenso. 

## <a name="next-steps"></a>Passos Seguintes
[Implemente](quick-create-marketplace.md) uma máquina virtual DCsv2-Series.


