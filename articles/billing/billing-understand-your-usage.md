---
title: Compreender a utilização detalhada do Azure | Documentos da Microsoft
description: Saiba como ler e entender as seções da sua utilização detalhada CSV para a sua subscrição do Azure
services: ''
documentationcenter: ''
author: bandersmsft
manager: alherz
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: banders
ms.openlocfilehash: a143fc6d9dbd78ae365f943a00ac9f8492d5e51c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57897151"
---
# <a name="understand-terms-on-your-microsoft-azure-detailed-usage-charges"></a>Compreender os termos na sua cobranças de utilização detalhados do Azure da Microsoft 

O ficheiro CSV de custos de utilização detalhada contém os custos de utilização de nível de diária e dos medidores para o período de faturação atual. 

Para obter o seu ficheiro de utilização detalhada, veja [como obter a faturação da nota fiscal e diário de dados de utilização do Azure](billing-download-azure-invoice-daily-usage-date.md).
Está disponível num formato de ficheiro de valores separados por vírgulas (. csv) que pode abrir num aplicativo de folha de cálculo. Se vir duas versões disponíveis, transfira a versão 2. Que é o formato de ficheiro mais recente.

Custos de utilização são o total **mensal** das cobranças uma subscrição. Os custos de utilização não levam em conta quaisquer créditos ou descontos.

>[!VIDEO https://www.youtube.com/embed/p13S350M2Vk]

## <a name="detailed-terms-and-descriptions-of-your-detailed-usage-file"></a>Termos detalhados e as descrições do seu ficheiro de utilização detalhada

As secções seguintes descrevem os termos de importantes exibidos na versão 2 do ficheiro de utilização detalhada.

### <a name="statement"></a>Declaração

A secção superior do ficheiro CSV de utilização detalhada mostra os serviços que utilizou durante o período de faturação do mês. A tabela seguinte lista os termos e as descrições apresentadas nesta secção.

| Termo | Descrição |
| --- | --- |
|Período de Faturação |O período de faturação quando eram usados os medidores |
|Categoria do Medidor |Identifica o serviço de nível superior para a utilização |
|Subcategoria do Medidor |Define o tipo de serviço do Azure que pode afetar a tarifa |
|Nome do Medidor |Identifica a unidade de medida para o medidor de consumo |
|Região do Medidor |Identifica a localização do datacenter para determinados serviços cujo preço é definido com base na localização do datacenter |
|SKU |Identifica o identificador de sistema exclusivo para cada Medidor do Azure |
|Unidade |Identifica a Unidade em que o serviço é cobrado. Por exemplo, GB, horas, 10 000 s. |
|Quantidade Consumida |A quantidade de medidor utilizado durante o período de faturação |
|Quantidade Incluída |A quantidade do medidor de que está incluída sem custos no período de faturação atual |
|Quantidade de Utilização Excedente |Mostra a diferença entre a quantidade consumida e a quantidade incluída. É-lhe cobrada durante este período. Para ofertas de pay as you go com sem quantidade incluída com a oferta, é este total equivale à quantidade consumida. |
|No Limite da Alocação |Mostra os custos de medidor são subtraídos da quantidade alocada associada com a sua oferta de 6 ou 12 meses. Custos de medidor são subtraídos por ordem cronológica. |
|Moeda |A moeda utilizada no período de faturação atual |
|Utilização Excedente |Mostra os custos de medidor que excedam a quantidade alocada associada com a sua oferta de 6 ou 12 meses |
|Tarifa de Alocação |Mostra a tarifa de alocação com base na totalidade do montante alocado associado à sua oferta de 6 ou 12 meses |
|Tarifa |A tarifa que lhe é cobrada por unidade faturável |
|Value |Mostra o resultado da multiplicação entre a coluna de quantidade de utilização excedida, a coluna tarifa. Se a quantidade consumida não exceder a quantidade incluída, não é sem encargos, nesta coluna. |

### <a name="daily-usage"></a>Utilização diária

A secção utilização diária do ficheiro CSV mostra detalhes de utilização que afetam as tarifas de faturas. A tabela seguinte lista os termos e as descrições apresentadas nesta secção.

| Termo | Descrição |
| --- | --- |
|Data de Utilização |A data em que foi utilizado o medidor |
|Categoria do Medidor |Identifica o serviço de nível superior que esta utilização corresponde |
|ID do Medidor |O identificador do medidor faturado que é utilizado para a utilização de faturação de preços |
|Subcategoria do Medidor |Define o tipo de serviço do Azure que pode afetar a tarifa |
|Nome do Medidor |Identifica a unidade de medida para o medidor de consumo |
|Região do Medidor |Identifica a localização do datacenter para determinados serviços cujo preço é definido com base na localização do datacenter |
|Unidade |Identifica a unidade que o medidor é cobrado. Por exemplo, GB, horas, 10 000 s. |
|Quantidade Consumida |A quantidade do medidor de que foi consumido para esse dia |
|Localização do Recurso |Identifica o datacenter onde o medidor está em execução |
|Serviço Consumido |O serviço de plataforma do Azure que utilizou |
|Grupo de Recursos |O grupo de recursos no qual o medidor implementado está em execução numa. <br/><br/>Para obter mais informações, veja [Descrição geral do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). |
|ID da Instância | O identificador para o medidor. <br/><br/> O identificador contém o nome que especificar para o medidor de quando foram criada. É o nome do recurso ou o ID de recurso completamente qualificado. Para obter mais informações, consulte [API do Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources). |
|Etiquetas | Etiqueta de que atribuir para o medidor. Utilize etiquetas para registos de faturação de grupo.<br/><br/>Por exemplo, pode utilizar etiquetas para distribuir os custos pelo departamento de que utiliza o medidor. Serviços que suportam a emissão de etiquetas são aprovisionados através da utilização de serviços de rede, armazenamento e máquinas virtuais a [API do Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources). Para obter mais informações, consulte [organizar os recursos do Azure com etiquetas](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/). |
|Informações Adicionais |Metadados de serviço específicos. Por exemplo, um tipo de imagem para uma máquina virtual. |
|Informações de Serviço 1 |O nome do projeto que o serviço pertence na sua subscrição |
|Informações de Serviço 2 |Campo legado que captura metadados opcionais do específico do serviço |

## <a name="how-do-i-make-sure-that-the-charges-in-my-detailed-usage-file-are-correct"></a>Como posso Certifique-se de que os encargos no meu arquivo de utilização detalhada estão corretos?
Se existe uma cobrança no seu ficheiro de utilização detalhada que deseja obter mais detalhes, consulte o artigo [compreender a sua fatura do Microsoft Azure.](./billing-understand-your-bill.md)

## <a name="external"></a>E encargos de serviços externos?
Serviços externos (também conhecido como pedidos de Marketplace) são fornecidos pelos fornecedores de serviço independentes e são faturados separadamente. Os encargos não aparecem da nota fiscal do Azure. Para obter mais informações, consulte [compreender os encargos de serviços externos do Azure](billing-understand-your-azure-marketplace-charges.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
