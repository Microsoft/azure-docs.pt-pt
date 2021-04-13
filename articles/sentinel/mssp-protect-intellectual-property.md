---
title: Proteger a propriedade intelectual gerida pelo prestador de serviços de segurança (MSSPs) em Azure Sentinel | Microsoft Docs
description: Saiba como os prestadores de serviços de segurança geridos (MSSPs) podem proteger a propriedade intelectual que criaram em Azure Sentinel.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.assetid: 10cce91a-421b-4959-acdf-7177d261f6f2
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2021
ms.author: bagol
ms.openlocfilehash: 1c15c341d85fae667ee23883043350340ad866b8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315533"
---
# <a name="protecting-mssp-intellectual-property-in-azure-sentinel"></a>Proteger a propriedade intelectual da MSSP em Azure Sentinel

Este artigo descreve os métodos que os prestadores de serviços de segurança geridos (MSSPs) podem usar para proteger a propriedade intelectual que desenvolveram em Azure Sentinel, tais como regras de análise Azure Sentinel, consultas de caça, livros de jogadas e livros de trabalho.

O método que escolher dependerá da forma como cada um dos seus clientes comprar o Azure; quer aja como Um Fornecedor de [Soluções Cloud (CSP)](#cloud-solutions-providers-csp)ou o cliente tem uma conta [De Acordo Empresarial (EA)/Pay-as-you-go (PAYG).](#enterprise-agreements-ea--pay-as-you-go-payg) As secções abaixo descrevem cada um destes métodos separadamente.

## <a name="cloud-solutions-providers-csp"></a>Fornecedores de Soluções de Nuvem (CSP)

Se está a revender o Azure como Fornecedor de Soluções cloud (CSP), está a gerir a subscrição Azure do cliente. Graças à [Admin-On-Behalf-Of (AOBO)](/partner-center/azure-plan-manage), os utilizadores do grupo de Agentes Administrativos do seu inquilino MSSP têm acesso ao proprietário acesso à subscrição Azure do cliente, e o cliente não tem acesso por padrão.

Se outros utilizadores do inquilino MSSP, fora do grupo de Agentes Administrativos, precisarem de aceder ao ambiente do cliente, recomendamos que utilize o [Farol Azure.](multiple-tenants-service-providers.md) O Azure Lighthouse permite-lhe conceder aos utilizadores ou grupos acesso a um âmbito específico, como um grupo de recursos ou subscrição, utilizando uma das funções incorporadas.

Se precisar de fornecer aos utilizadores clientes acesso ao ambiente Azure, recomendamos que lhes conceda acesso a nível de *grupo de recursos,* em vez de toda a subscrição, para que possa mostrar/esconder partes do ambiente conforme necessário.

Por exemplo:

- Pode conceder ao cliente acesso a vários grupos de recursos onde as suas aplicações estão localizadas, mas ainda assim manter o espaço de trabalho Azure Sentinel num grupo de recursos separado, onde o cliente não tem acesso.

- Utilize este método para permitir que os clientes vejam livros e livros de jogadas selecionados, que são recursos separados que podem residir no seu próprio grupo de recursos.

Mesmo com a concessão de acesso ao nível do grupo de recursos, os clientes continuarão a ter acesso aos dados de registo dos recursos a que podem aceder, como registos a partir de um VM, mesmo sem acesso ao Azure Sentinel. Para obter mais informações, consulte [Gerir o acesso aos dados do Azure Sentinel por recurso.](resource-context-rbac.md)

> [!TIP]
> Se precisar de fornecer aos seus clientes acesso a toda a subscrição, poderá querer ver a orientação nos [Acordos empresariais (EA) / Pay-as-you-go (PAYG)](#enterprise-agreements-ea--pay-as-you-go-payg).
>

### <a name="sample-azure-sentinel-csp-architecture"></a>Experimente a arquitetura CSP do Azure Sentinel

A imagem a seguir descreve como as permissões descritas na [secção anterior](#cloud-solutions-providers-csp) podem funcionar ao fornecer acesso aos clientes CSP:

:::image type="content" source="media/mssp-protect-intellectual-property/csp-customers.png" alt-text="Proteja a sua propriedade intelectual Azure Sentinel com clientes CSP.":::

Nesta imagem:

- Os utilizadores concedidos com o Acesso **do Proprietário** à subscrição CSP são os utilizadores do grupo de Agentes Admin, no inquilino MSSP Azure AD.
- Outros grupos do MSSP têm acesso ao ambiente do cliente através do Farol Azure.
- O acesso dos clientes aos recursos Azure é gerido pela Azure RBAC ao nível do grupo de recursos.

    Isto permite que os MSSPs escondam os componentes do Azure Sentinel conforme necessário, como as Regras de Analítica e As Consultas de Caça.

Para mais informações, consulte também a [documentação do Farol Azure.](/azure/lighthouse/concepts/cloud-solution-provider)

## <a name="enterprise-agreements-ea--pay-as-you-go-payg"></a>Acordos empresariais (EA) / Pay-as-you-go (PAYG)

Se o seu cliente estiver a comprar diretamente à Microsoft, o cliente já tem acesso total ao ambiente Azure, e não pode esconder nada que esteja na subscrição do Azure do cliente.

Em vez disso, proteja a sua propriedade intelectual que desenvolveu em Azure Sentinel da seguinte forma, dependendo do tipo de recurso que precisa para proteger:

### <a name="analytics-rules-and-hunting-queries"></a>Regras de análise e consultas de caça

As regras de análise e as consultas de caça estão ambas contidas no Azure Sentinel, pelo que não podem ser separadas do espaço de trabalho do Azure Sentinel.

Mesmo que um utilizador tenha apenas permissões do Azure Sentinel Reader, ainda poderá ver a consulta. Neste caso, recomendamos hospedar as suas regras de Analytics e consultas de caça no seu próprio inquilino MSSP, em vez do inquilino do cliente.

Para isso, você precisará de um espaço de trabalho no seu próprio inquilino com Azure Sentinel habilitado, e você também precisará ver o espaço de trabalho do cliente através [do Farol Azure.](multiple-tenants-service-providers.md)

Para criar uma regra analítica ou consulta de caça no inquilino MSSP que faz referência aos dados no cliente inquilino, deve utilizar a declaração da `workspace` seguinte forma:

```kql
workspace('<customer-workspace>').SecurityEvent
| where EventID == ‘4625’
```

Ao adicionar uma `workspace` declaração às suas regras de análise, considere o seguinte:

- **Sem alertas no espaço de trabalho do cliente.** As regras criadas desta forma, não criarão alertas ou incidentes no espaço de trabalho do cliente. Tanto os alertas como os incidentes existirão apenas no seu espaço de trabalho MSSP.

- **Criar alertas separados para cada cliente.** Ao utilizar este método, recomendamos também que utilize regras de alerta separadas para cada cliente e deteção, uma vez que a declaração do espaço de trabalho será diferente em cada caso.

    Pode adicionar o nome do cliente ao nome da regra de alerta para identificar facilmente o cliente onde o alerta é desencadeado. Alertas separados podem resultar num grande número de regras, que é possível que queira gerir utilizando scripts, ou [Azure Sentinel como Código](https://techcommunity.microsoft.com/t5/azure-sentinel/deploying-and-managing-azure-sentinel-as-code/ba-p/1131928).

    Por exemplo:

    :::image type="content" source="media/mssp-protect-intellectual-property/mssp-rules-per-customer.png" alt-text="Crie regras separadas no seu espaço de trabalho MSSP para cada cliente.":::

- **Criar espaços de trabalho MSSP separados para cada cliente.** A criação de regras separadas para cada cliente e deteção pode fazer com que atinja o número máximo de regras de análise para o seu espaço de trabalho (512). Se tem muitos clientes e espera atingir este limite, pode querer criar um espaço de trabalho MSSP separado para cada cliente.

    Por exemplo:

    :::image type="content" source="media/mssp-protect-intellectual-property/mssp-rules-and-workspace-per-customer.png" alt-text="Crie um espaço de trabalho e regras no seu inquilino MSSP para cada cliente.":::

> [!IMPORTANT]
> A chave para usar este método com sucesso é usar a automatização para gerir um grande conjunto de regras em todos os seus espaços de trabalho.
>
> Para mais informações, consulte [as regras de análise cross-workspace](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-cross-workspace-analytics-rules/ba-p/1664211)
>

### <a name="workbooks"></a>Livros

Se desenvolveu um livro da Azure Sentinel que não quer que o seu cliente copie, apresente o livro no seu inquilino MSSP. Certifique-se de que tem acesso aos espaços de trabalho do seu cliente através do Farol Azure e, em seguida, certifique-se de modificar o livro para utilizar esses espaços de trabalho do cliente.

Por exemplo:

:::image type="content" source="media/mssp-protect-intellectual-property/cross-workspace-workbook.png" alt-text="Livros de espaço de trabalho cruzados":::

Para mais informações, consulte [os livros de cross-workspace.](extend-sentinel-across-workspaces-tenants.md#cross-workspace-workbooks)

Se quiser que o cliente possa visualizar as visualizações do livro, mantendo o código em segredo, recomendamos que exporte o livro para o Power BI.

Exportando o seu livro para Power BI:

- **Torna as visualizações de livros mais fáceis de partilhar.** Pode enviar ao cliente um link para o painel Power BI, onde podem visualizar os dados relatados, sem que sejam necessárias permissões de acesso ao Azure.
- **Permite agendamento**. Configure o Power BI para enviar e-mails periodicamente que contenham uma imagem do painel de instrumentos para esse tempo.

Para obter mais informações, consulte [os dados de registo do Monitor importância para o Power BI](/azure/azure-monitor/visualize/powerbi).

### <a name="playbooks"></a>Manuais de procedimentos

Pode proteger os seus livros de jogadas da seguinte forma, dependendo de onde foram criadas as regras analíticas que desencadeiam o livro de jogadas:

- **Regras de análise criadas no espaço de trabalho MSSP.**  Certifique-se de criar os seus livros de jogadas no inquilino MSSP, e que obtém todos os dados de incidentes e alerta do espaço de trabalho MSSP. Pode anexar os livros de reprodução sempre que criar uma nova regra no seu espaço de trabalho.

    Por exemplo:

    :::image type="content" source="media/mssp-protect-intellectual-property/rules-in-mssp-workspace.png" alt-text="Regras criadas no espaço de trabalho mssp.":::

- **Regras de análise criadas no espaço de trabalho do cliente.** Utilize o Farol Azure para anexar regras de análise do espaço de trabalho do cliente a um livro de jogadas alojado no seu espaço de trabalho MSSP. Neste caso, o livro de jogadas recebe os dados de alerta e incidente, e qualquer outra informação do cliente, a partir do espaço de trabalho do cliente.

    Por exemplo:

    :::image type="content" source="media/mssp-protect-intellectual-property/rules-in-customer-workspace.png" alt-text="Regras criadas no espaço de trabalho do cliente.":::

Em ambos os casos, se o livro de jogadas precisar de aceder ao ambiente Azure do cliente, utilize um utilizador ou um chefe de serviço que tenha esse acesso através do Farol.

No entanto, se o livro de jogadas precisar de aceder a recursos não-Azure no inquilino do cliente, como a Azure AD, Office 365 ou Microsoft 365 Defender, terá de criar um principal de serviço com permissões adequadas no inquilino do cliente e, em seguida, adicionar essa identidade no livro de jogadas.

> [!NOTE]
> Se utilizar regras de automatização juntamente com os seus livros de jogadas, deve definir as permissões de regras de automatização no grupo de recursos onde os playbooks vivem.
> Para obter mais informações, consulte [permissões para que as regras de automação para executar livros de brincadeiras](automate-incident-handling-with-automation-rules.md#permissions-for-automation-rules-to-run-playbooks).
>

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte:

- [Manual Técnico Azure Sentinel para MSSPs](https://cloudpartners.transform.microsoft.com/download?assetname=assets/Azure-Sentinel-Technical-Playbook-for-MSSPs.pdf&download=1)
- [Gerir vários inquilinos em Azure Sentinel como MSSP](multiple-tenants-service-providers.md)
- [Alargar o Azure Sentinel através de áreas de trabalho e inquilinos](extend-sentinel-across-workspaces-tenants.md)
- [Tutorial: Visualizar e monitorizar os seus dados](tutorial-monitor-your-data.md)
- [Tutorial: Configurar respostas automáticas de ameaças em Azure Sentinel](tutorial-respond-threats-playbook.md)
