---
title: Definições de informações do vendedor
description: Fornece definições para muitos dos termos que você encontrará em informações do vendedor
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 2571f916f6efc61772b2734119e197996e2ffe3c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73809115"
---
<a name="seller-insights-definitions"></a>Definições de informações do vendedor
=======================

A tabela a seguir fornece definições para muitos dos termos usados no vendedor insights.

|  **Prazo**                 |  **Definição**                                                                                                                              |
|  --------------------------------------------    |  ---------------------------------------------------------------------------------------------------------------------------------           |
| **Tipo de licença do Azure**                               | O tipo de contrato de licenciamento usado pelos clientes para comprar o Azure.  Também conhecido como canal.                                                  |
| **Tipo de licença do Azure: provedor de soluções na nuvem**      | O cliente final adquiri o Azure e seu Marketplace oferece por meio de seu provedor de soluções na nuvem, que atua como seu revendedor.                 |
| **Tipo de licença do Azure: Enterprise**                   | O cliente final compra o Azure e sua oferta do Marketplace por meio de um Enterprise Agreement, assinados diretamente com a Microsoft.                  |
| **Tipo de licença do Azure: Enterprise por meio do revendedor**  | O cliente final compra o Azure e sua oferta do Marketplace por meio de um revendedor que facilita seus Enterprise Agreement com a Microsoft.     |
| **Tipo de licença do Azure: pague conforme o uso**                | O cliente final compra o Azure e sua oferta do Marketplace por meio de um contrato "pré-pago", assinado diretamente com a Microsoft.                |
| **Valor da cobrança (CC)**                              | O valor cobrado para o cliente, na *moeda do cliente* (CC) para cobrança.                                                                 |
| **Valor da cobrança (PC)**                               | O valor cobrado para o cliente, na *moeda do pagamento* (PC).                                                                      |
| **Data de cobrança**                                      | A data em que a cobrança do cliente foi calculada (normalmente logo após o período de uso).                                             |
| **Nome da instância de nuvem**                              | O Microsoft Cloud em que ocorreu uma implantação de VM.                                   |
| **Nome da instância de nuvem: Azure global**                | A nuvem global da Microsoft pública.                           |
| **Nome da instância de nuvem: Azure governamental**        | Nuvens Microsoft específicas do governo para um dos seguintes governos: China, Alemanha ou a Estados Unidos da América.                           |
| **Status da coleção**  | Para um encargo específico, o status mais recente do ciclo de vida de cobrança e coleção.  Por exemplo, o status pode ser coleta em andamento, coletada ou reembolso.                  |
| **Tamanho do núcleo**  | O número de núcleos virtuais apresentados pela máquina virtual na qual a oferta está sendo executada.               |
| **Cidade do cliente**  | O nome da cidade fornecida pelo cliente. Isso pode ser diferente da cidade na assinatura do Azure de um cliente.  |
| **Idioma de comunicação do cliente**        | A linguagem preferida pelo cliente para comunicação.                                  |
| **Nome da empresa do cliente**                  | O nome da empresa fornecido pelo cliente. Isso pode ser diferente da cidade na assinatura do Azure de um cliente.                                  |
| **País do cliente**                       | O nome do país/região fornecido pelo cliente.  Isso pode ser diferente do país/região na assinatura do Azure de um cliente.                               |
| **Moeda do cliente (CC)**                 | A moeda preferida pelo cliente para preços e cobrança.                            |
| **Email do cliente**                         | O endereço de email fornecido pelo cliente final.  Isso pode ser diferente do endereço de email na assinatura do Azure de um cliente.                   |
| **Nome do cliente**                              | O nome fornecido pelo cliente.  Isso pode ser diferente do nome fornecido na assinatura do Azure de um cliente.                    |
| **Sobrenome do cliente**                               | O último nome fornecido pelo cliente.  Isso pode ser diferente do nome fornecido na assinatura do Azure de um cliente.                    |
| **ID do cliente**                               | O identificador exclusivo atribuído a um cliente.  Um cliente pode ter zero ou mais assinaturas do Azure Marketplace.                    |
| **Tipo de pagamento do cliente**                            | O tipo de instrumento de pagamento usado pelo cliente.  Por exemplo, pode ser cartão, fatura ou cheque eletrônico.                             |
| **CEP do cliente**                             | O CEP fornecido pelo cliente.  Isso pode ser diferente do código postal fornecido na assinatura do Azure de um cliente.                                                                 |
| **Estado do cliente**                                   | O estado (endereço) fornecido pelo cliente.  Isso pode ser diferente do último nome fornecido na assinatura do Azure de um cliente.                                                                 |
| **Data de aquisição**                                    | A primeira data em que o cliente comprou qualquer oferta publicada por você.                 |
| **Data de perda**                                        | A última data em que o cliente cancelou o último de todas as ofertas adquiridas anteriormente.                                                         |
| **Carga estendida estimada (CC)**                   | O encargo estendido estimado para a quantidade de unidades de uso de uma determinada SKU (na moeda do cliente). Esse valor pode não ser exato devido a erros de arredondamento ou truncamento.                            |
| **Carga estendida estimada (PC)**                   | O encargo estendido estimado para a quantidade de unidades de uso de um determinado SKU, com base na conversão de troca estrangeira no uso da data, é calculado (na moeda do editor). Esse valor pode não ser exato devido a erros de arredondamento ou truncamento.                  |
| **Pagamento estimado (PC)**                            | O pagamento estimado para a quantidade de unidades de uso de um determinado SKU, com base na conversão de trocas estrangeiras na data em que o uso é calculado (na moeda do editor). Esse valor pode não ser exato devido a erros de arredondamento ou truncamento.                        |
| **Preço estimado (PC)**                             | O preço estimado para uma unidade de uso de uma determinada SKU, com base na conversão de troca estrangeira no uso da data, é calculado (na moeda do editor). Esse valor pode não ser exato devido a erros de arredondamento ou truncamento.                                             |
| **É novo cliente**                                  | O valor informará se é um novo cliente que está consumindo ou comprando uma ou mais ofertas do Marketplace pela primeira vez (ou não).  O valor será "Sim" se for o mesmo mês de calendário para "data de aquisição".  O valor será "não" se o cliente tiver comprado uma de suas ofertas antes do relatório do mês do calendário.                                                       |
| **É versão prévia do SKU**                                  | O valor informará se você marcou a SKU como "visualização". O valor será "Sim" se o SKU tiver sido marcado de acordo e somente as assinaturas do Azure autorizadas por você puderem implantar e usar essa imagem.  O valor será "no" se o SKU não tiver sido identificado como "Preview".                                          |
| **É aceitar contato promocional**                   | O valor permitirá que você saiba se o cliente optou proativamente para contato promocional dos editores. Neste momento, não estamos apresentando a opção aos clientes, portanto, indicamos "não" no tabuleiro. Depois que esse recurso for implantado, começaremos a atualizar de acordo.                                          |
| **Tipo de licença do Marketplace**                         | O método de cobrança da oferta do Marketplace.                             |
| **Tipo de licença do Marketplace: cobrado por meio do Azure**   | A Microsoft é o seu agente para esta oferta do Marketplace e cobra os clientes em seu nome. (PAYG de cartão de crédito ou nota fiscal empresarial)       |
| **Tipo de licença do Marketplace: traga sua própria licença** | A VM requer uma chave de licença fornecida pelo cliente para implantar. A Microsoft não cobra os clientes pelo uso dessas ofertas do Marketplace. |
| **Tipo de licença do Marketplace: gratuito**                   | A oferta do Marketplace está configurada para ser gratuita para todos os usuários. A Microsoft não cobra os clientes pelo uso dessa oferta do Marketplace.    |
| **Tipo de licença do Marketplace: Microsoft como revendedor**  | A Microsoft é seu revendedor para esta oferta do Marketplace.                               |
| **Nome da empresa de assinatura do Marketplace**  | O nome da empresa fornecido pelo cliente em sua assinatura do Azure.                           |
| **ID da assinatura do Marketplace**            | O identificador exclusivo associado à assinatura do Azure que o cliente usou para comprar sua oferta do Marketplace.  Era anteriormente a GUID de assinatura do Azure.  Em alguns casos, como uma ordem SAAS, nenhuma assinatura é necessária.           |
| **Microsoft Fee (CC)**                               | A taxa da Microsoft na transação na moeda do cliente.                              |
| **Nome da oferta**                                | O nome da oferta do Marketplace.                                                             |
| **Tipo de oferta**                                | O tipo de oferta de Microsoft Marketplace.  Por exemplo, ele poderia ser aplicativo gerenciado, licenças de máquina virtual ou contêiner.                           |
| **Data de cancelamento do pedido**                         | A data em que a ordem do Marketplace foi cancelada.                                                       |
| **ID do pedido**                                  | O identificador exclusivo da ordem do cliente para o serviço do Marketplace.  As ofertas baseadas em uso da máquina virtual não estão associadas a um pedido.                 |
| **Data de compra do pedido**                       | A data em que a ordem do Marketplace foi criada.                                                        |
| **Quantidade do pedido**                       | A quantidade de pedidos para a mesma SKU associada a uma assinatura do Azure.  Para pedidos de SAAS, ele representa o número de assentos associados a essa instância de um pedido.                                            |
| **Status do pedido**                              | O status de uma ordem do Marketplace no momento em que os dados foram atualizados pela última vez.  Por exemplo, a ordem pode estar ativa ou cancelada.             |
| **Status do pedido: ativo**                           | O cliente comprou um pedido e não cancelou seu pedido.   |
| **Status do pedido: cancelado**                        | O cliente comprou um pedido anteriormente e, subsequentemente, cancelou seu pedido.        |
| **Valor do pagamento (PC)**                             | O valor pago a você, em sua moeda de pagamento preferida (PC).                                       |
| **Moeda do pagamento (PC)**                           | A moeda usada para os pagamentos.                                                                |
| **Data do pagamento**                                    | A data em que a solicitação de pagamento foi enviada da Microsoft para sua instituição financeira escolhida.                                                                         |
| **Status do pagamento**                                  | O status do pagamento no momento em que os dados foram atualizados pela última vez.                                   |
| **Status do pagamento: em andamento**               | A transação não está pronta para pagamento. (Para obter mais informações, consulte status da coleção)                             |
| **Status do pagamento: pago**                          | A transação foi incluída em um cálculo de pagamento anterior. Os valores positivos são pagos e os valores negativos são feitos na sub-rede contra o valor total devido. |
| **Status do pagamento: pagamento futuro**                   | A transação está pronta para pagamento e será incluída no próximo cálculo de pagamento disponível.                      |
| **Preço (CC)**                                   | O preço de uma unidade de uso para uma determinada SKU (na moeda do cliente).                                                                  |
| **Email do provedor**                                 | O endereço de email do provedor envolvido na relação entre a Microsoft e o cliente final. Se o cliente for uma empresa por meio do revendedor, será o revendedor.  Se um CSP (provedor de soluções de nuvem) estiver envolvido, ele será o CSP.                                                                 |
| **Nome do provedor**                                 | O nome do provedor envolvido na relação entre a Microsoft e o cliente final. Se o cliente for uma empresa por meio do revendedor, será o revendedor.  Se um CSP (provedor de soluções de nuvem) estiver envolvido, ele será o CSP.                                                                 |
| **URI de recurso**                                  | O caminho em que o artefato de implantação foi implantado.                                            |
| **SKU**                                           | Nome do SKU, conforme definido durante a publicação. Uma oferta pode ter muitas SKUs, mas uma SKU só pode ser associada a uma única oferta.              |
| **Tipo de cobrança de SKU**                                 | O método de cobrança da SKU.                                                                                                             |
| **Tipo de cobrança de SKU: traga sua própria licença**         | A VM ou o serviço requer uma chave de licença fornecida pelo cliente para implantar. A Microsoft não cobra os clientes pelo uso dessas ofertas do Marketplace.   |
| **Tipo de cobrança de SKU: Microsoft como revendedor**          | A Microsoft é seu revendedor para esta SKU.   |
| **Tipo de cobrança de SKU: gratuito**                           | A SKU está configurada para ser gratuita para todos os usuários. A Microsoft não cobra os clientes pelo uso desta SKU.                           |
| **Tipo de cobrança de SKU: pago**                           | A Microsoft é seu agente para esta SKU e cobra os clientes em seu nome. (PAYG de cartão de crédito ou nota fiscal empresarial)                   |
| **Tipo de cobrança de SKU: avaliação**                          | O cliente está em seu período de avaliação e será convertido para pago se não cancelar ou excluir.                                         |
| **Valor do imposto (CC)**                                  | O valor do imposto aplicado à fatura do cliente na moeda do cliente (CC).                                                               |
| **Data da transação**                                 | A data da transação registrada em seu relatório de pagamento.                                                                           |
| **Tipo de transação**                               | O tipo de transação que está sendo relatado.  Por exemplo, isso pode ser um ajuste de encargo, reembolso ou pagamento.                                                                                        |
| **Data de término da avaliação**                                 | A data em que o período de avaliação para esta ordem será encerrado ou finalizado.                                                                           |
| **Data de uso**                                       | A data em que o uso do cliente ocorreu.                                                                                                             |
| **Quantidade de uso**                                       | O uso do cliente relatado da SKU. Para imagens de VM, os registros de uso representam o uso do período relatado para esse tamanho de VM e SKU.                                               |
| **Referência de uso**                                       | O identificador de um ou mais dias de uso do cliente para uma determinada SKU associada a uma entrada no relatório de pagamento.                                               |
| **Tipo de uso**                                       | Uma descrição do uso que está sendo medido. (Uso normalizado ou uso bruto)                                                              |
| **Tipo de uso: uso normalizado**                | Horas de uso normalizadas para considerar o número de núcleos de VM envolvidos no uso: [número de núcleos de VM] x [horas de uso bruto]. As VMs designadas como "SHAREDCORE" usam 1/6 (ou 0,1666) como o multiplicador [número de núcleos de VM].                                        |
| **Tipo de uso: uso bruto**                        | Horas de uso para sua oferta do Marketplace.  Ele é apresentado em termos de horas de "calendário".  É o número de vezes que as máquinas virtuais estão em execução.                           |
| **Unidades de uso**                                   | A unidade de medida para o uso declarado. As VMs são sempre medidas com unidades de medida por hora.                               |
| **Tamanho da VM**                                       | Representa o tamanho do hardware da máquina virtual alinhado com a oferta do Azure. Os exemplos incluem `Basic_A0`, `Standard_A11`, `Standard_D12`e `Standard_G4`.   |
|  |  |


