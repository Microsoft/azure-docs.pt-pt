---
title: Provisão BareMetal para oráculo
description: Saiba mais sobre o fornecimento da sua Infraestrutura BareMetal para a Oracle.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 45ca2bf8bfc61c3820b4b14daa998e2e82e972fa
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559219"
---
# <a name="provision-baremetal-for-oracle"></a>Provisão BareMetal para oráculo

Neste artigo, vamos ver como providenciar as suas instâncias de Infraestrutura BareMetal para cargas de trabalho da Oracle. 

O primeiro passo para a provisionar as suas instâncias BareMetal é trabalhar com o seu Microsoft CSA. Eles vão ajudá-lo com base nas suas necessidades específicas de carga de trabalho e na arquitetura que está a implementar, seja num único caso, Um Nó RAC ou RAC. Para obter mais informações sobre estas topologias, consulte [Arquitetura da Infraestruturas BareMetal para o oráculo.](oracle-baremetal-architecture.md)

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Uma subscrição ativa do Azure
> * Contrato de suporte da Microsoft Premier
> * Licenças para Red Hat Enterprise Linux 7.6
> * Contrato de apoio da Oracle 
> * Licenças e componentes de instalação de software para a Oracle
> * ExpressRoute conectado **no local ao Azure** **(Opcionalmente,** configurar ExpressRoute Global Reach para conectividade direta desde as instalações até à Base de Dados do Oráculo)   
> * Rede virtual
> * Criação do gateway
> * Máquinas virtuais (VMs) na rede virtual (caixas de salto)

## <a name="information-to-provide-microsoft-operations"></a>Informações para fornecer operações da Microsoft

Terá de fornecer as seguintes informações à sua CSA:

1. Espaço de endereço de rede virtual. Esta gama deve ser de sub-rede /24; por exemplo, 10.11.0.0/24.
2. P2P alcance. Esta gama deve ser uma sub-rede /29; por exemplo, 10.12.0.0/29.
3. Piscina de endereços IP do servidor. A gama recomendada é /24; por exemplo, 10.13.0.0/24.
4. Endereço IP do servidor. Escolha um endereço IP no conjunto de endereços IP do Servidor.

    > [!Note] 
    > Os primeiros trinta endereços IP são reservados para a configuração de infraestrutura da Microsoft. Assim, neste exemplo, o seu primeiro endereço IP disponível para uma lâmina seria 10.13.0.30.

5. A Região de Azure é necessária; por exemplo, West US2.
6. A Infraestrutura BareMetal SKU necessária; por exemplo, S192 (duas máquinas).

## <a name="storage-requirements"></a>Requisitos de armazenamento

Trabalhe com o seu representante da CSA para as suas necessidades de armazenamento durante o pedido de provisionamento, incluindo as necessidades de armazenamento esperadas com base no crescimento futuro. O armazenamento adicionado está em incrementos de 1-TB.

Para volumes, seguimos o [padrão optimal de Arquitetura Flexível (OFA)](https://docs.oracle.com/en/database/oracle/oracle-database/19/ladbi/about-the-optimal-flexible-architecture-standard.html#GUID-6619CDB7-9667-426E-8471-5A996707D093)da Oracle, com nível básico e configuração empresarial. Se tiver requisitos de armazenamento personalizados que não o tamanho padrão de "T-shirt", faça o seu pedido personalizado através do seu CSA.

| Configuração básica (POC/ Teste) | Description | Pequeno | Médio | Grande |
| --- | --- | --- | --- | --- |
| /u01 | Binários oráculos | 500 GB | 500 GB | 500 GB |
| /u02 | Ler Intensivo/Administrador | 500 GB | 1 TB | 5 TB |
| /u03 | Escrever Intensivo/Logs | 500 GB | 1 TB | 5 TB |
| /u09 | Backup | 5 TB | 10 TB | 15 TB |

| Configuração da Empresa | Description | Pequeno | Médio | Grande | Extra Grande |
| --- | --- | --- | --- | --- | --- |
| /u01 | Binários oráculos | 500 GB | 500 GB | 500 GB | 500 GB |
| /u02 | Administrador | 100 GB | 100 GB | 100 GB | 100 GB |
| /u10 a /u59 | Ler Intensivo | 500 GB | 5 TB | 10 TB | 20 TB |
| /u60 a /u89 | Escrever Intensivo | 500 GB | 5 TB | 10 TB | 20 TB |
| /u90 para /u91 | Redo Registos | 500 GB | 500 GB | 1 TB | 1 TB |
| /u95 | Arquivo | 10 TB | 10 TB | 20 TB | 20 TB |
| /u98 | Backup | 25 TB | 25 TB | 50 TB | 50 TB |

## <a name="next-step"></a>Passo seguinte

Saiba mais sobre a Infraestruturas BareMetal para o Oráculo.

> [!div class="nextstepaction"]
> [O que é a Infraestrutura BareMetal em Azure?](../../concepts-baremetal-infrastructure-overview.md)
