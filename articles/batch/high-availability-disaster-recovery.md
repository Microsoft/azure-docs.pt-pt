---
title: Alta disponibilidade e recuperação de desastres – lote do Azure | Microsoft Docs
description: Saiba como projetar seu aplicativo do lote para uma interrupção regional
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/29/2019
ms.author: lahugh
ms.openlocfilehash: 11446f5f23d470234b5dba641dc16e0bf71d9b72
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094037"
---
# <a name="design-your-application-for-high-availability"></a>Conceber a sua aplicação para elevada disponibilidade

O lote do Azure é um serviço regional. O lote está disponível em todas as regiões do Azure, mas quando uma conta do lote é criada, ela deve ser associada a uma região. Todas as operações da conta do lote são aplicadas a essa região. Por exemplo, pools e VMs (máquinas virtuais) associadas são criados na mesma região que a conta do lote.

Ao criar um aplicativo que usa o lote, você deve considerar a possibilidade de o lote não estar disponível em uma região. É possível encontrar uma situação rara em que há um problema com a região como um todo, o serviço do lote inteiro na região ou um problema com sua conta do lote específica.

Se o aplicativo ou a solução que usa o lote sempre precisar estar disponível, ele deverá ser criado para failover para outra região ou sempre ter a divisão de carga de trabalho entre duas ou mais regiões. Ambas as abordagens exigem pelo menos duas contas do lote, com cada conta localizada em uma região diferente.

## <a name="multiple-batch-accounts-in-multiple-regions"></a>Várias contas do lote em várias regiões

Usar várias contas do lote em várias regiões fornece a capacidade de o aplicativo continuar em execução se uma conta do lote em outra região ficar indisponível. Usar várias contas é especialmente importante se seu aplicativo precisar estar altamente disponível.

Em alguns casos, um aplicativo pode ser projetado para sempre usar duas ou mais regiões. Por exemplo, quando você precisa de uma quantidade considerável de capacidade, o uso de várias regiões pode ser necessário para lidar com um aplicativo de grande escala ou para o crescimento futuro.

## <a name="design-considerations-for-providing-failover"></a>Considerações de design para fornecer failover

Um ponto importante a ser considerado ao fornecer a capacidade de failover para uma região alternativa é que todos os componentes em uma solução precisam ser considerados; Não é suficiente simplesmente ter uma segunda conta do lote. Por exemplo, na maioria dos aplicativos do lote, uma conta de armazenamento do Azure é necessária, com a conta de armazenamento e a conta do lote que precisam estar na mesma região para desempenho aceitável.

Considere os seguintes pontos ao criar uma solução que pode fazer failover:

- Crie previamente todas as contas necessárias em cada região, como a conta do lote e a conta de armazenamento. Em geral, não há nenhuma cobrança por ter contas criadas, somente quando há dados armazenados ou a conta é usada.
- Verifique se as cotas estão definidas nas contas antes do tempo, para que você possa alocar o número necessário de núcleos usando a conta do lote.
- Use modelos e/ou scripts para automatizar a implantação do aplicativo em uma região.
- Manter binários de aplicativos e dados de referência atualizados em todas as regiões. Manter-se atualizado garantirá que a região possa ser colocada online rapidamente sem precisar esperar o upload e a implantação de arquivos. Por exemplo, se um aplicativo personalizado a ser instalado em nós de pool for armazenado e referenciado usando pacotes de aplicativos do lote, quando uma nova versão do aplicativo for produzida, ela deverá ser carregada para cada conta do lote e referenciada pela configuração do pool (ou tornar a nova versão a versão padrão).
- No aplicativo que chama o lote, o armazenamento e quaisquer outros serviços, facilmente comutando clientes ou a carga para a região diferente.
- Uma prática recomendada para garantir que um failover será bem-sucedida é fazer a alternância com frequência para uma região alternativa como parte da operação normal. Por exemplo, com duas implantações em regiões separadas, alternando para a região alternativa todos os meses.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre como criar contas do lote com o [portal do Azure](batch-account-create-portal.md), o [CLI do Azure](cli-samples.md), o [PowerShell](batch-powershell-cmdlets-get-started.md)ou a API de [Gerenciamento do lote](batch-management-dotnet.md).
- As cotas padrão são associadas a uma conta do lote; [Este artigo](batch-quota-limit.md) detalha os valores de cota padrão e descreve como as cotas podem ser aumentadas.
