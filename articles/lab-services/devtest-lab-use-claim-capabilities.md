---
title: Utilize capacidades de reclamação em Laboratórios Azure DevTest / Microsoft Docs
description: Conheça diferentes cenários para utilizar capacidades de reclamação/não reclamação de Laboratórios Azure DevTest
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2019
ms.author: spelluru
ms.openlocfilehash: a15148260bccadc59966c86031100f0e0332b0f9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67861433"
---
# <a name="use-claim-capabilities-in-azure-devtest-labs"></a>Utilize capacidades de reclamação em Laboratórios Azure DevTest
O serviço Azure DevTest Labs melhora a eficácia e eficiência dos desenvolvedores e testadores. Este artigo centra-se na capacidade de reivindicar ou não reivindicar máquinas virtuais em Azure DevTest Labs. Também enumera várias formas de esta funcionalidade melhorar a experiência do utilizador. Antes de olhar para diferentes cenários onde esta funcionalidade pode ser usada, vamos ver o que é **reivindicar** e como funciona.

## <a name="claimable-machines"></a>Máquinas reivíveis
Uma máquina reivível é uma máquina virtual (VM) que é criada num laboratório sem um dono. Uma vez reclamada a máquina, o utilizador tem uma gama completa de opções para esse VM. Quando um utilizador reclama uma máquina, são feitas algumas alterações. O VM é transferido da lista de **máquinas virtuais reivíveis** para a lista **das minhas máquinas virtuais** no portal Azure. 

O utilizador pode ligar-se ao VM, personalizar artefactos, reiniciar, parar ou não reclamar a máquina. Há algumas maneiras de tornar um VM reclamado:

- Crie uma máquina e não a reclame para que se mova para a piscina reivindicada. 
- Crie um VM e coloque na piscina partilhada utilizando [configurações avançadas](https://azure.microsoft.com/updates/azure-devtest-labs-claim-lab-vms-from-a-shared-pool/).

Há dois casos em que as capacidades de reclamação/não-reivindicação podem ser utilizadas eficazmente. O primeiro caso requer mais premeditação e planeamento, para ser projetado e executado corretamente. E a segunda é mais situacional. Seguem-se alguns exemplos dos diferentes casos.

## <a name="designed-use-of-claimable-machines"></a>Utilização concebida de máquinas reempreensíveis

- **Desenvolvimento de software /testes:** Permitir que os desenvolvedores ou testadores sejam mais produtivos por ter máquinas configuradas prontas e num estado não reclamado. Ter um conjunto de VMs com configurações diferentes, ferramentas necessárias, e com o código mais recente permite que os utilizadores reclamem um VM e comecem a trabalhar sem terem de passar o tempo a montar uma máquina. Antes de os VMs serem reivindicados, as máquinas são aprovisionadas, mas estão a desligar minimizando o custo de ter máquinas que são usadas com menos frequência. Quando os VMs são necessários, um utilizador simplesmente reclama o VM, que inicia a máquina. A opção não reclamada não é tão útil neste caso, uma vez que a criação de um novo VM é muitas vezes mais fácil e barata.
- **Sala de aula/Laboratórios:** Ter VMs pré-configurados para uma aula ou um laboratório para que os alunos possam ligar-se imediatamente a uma máquina usando o portal Azure.  Uma vez que um aluno reclama um VM, o laboratório garante que ninguém pode reclamar a mesma máquina. Automatizar este processo garante que o número necessário de máquinas com o ambiente especificado está disponível. Se os alunos não aparecerem ou se atrasarem, as máquinas não reclamadas podem ser mantidas disponíveis até que a sessão acabe com o mínimo de custo. A opção não reclamada não é tão eficaz neste cenário, uma vez que o VM está num estado desconhecido quando o utilizador anterior é feito.
- **Demonstrações:** Utilize máquinas para demonstrações, onde as máquinas do laboratório são montadas com ambientes específicos. Esta capacidade é útil onde várias pessoas podem estar a fazer uma demonstração ao mesmo tempo ou em momentos aleatórios, como numa conferência. A opção não reclamada pode ser útil nesta situação, uma vez que a demonstração não deve alterar o estado da máquina, permitindo que os utilizadores devolam um VM de volta à piscina reivindicada para a próxima demonstração. Com a máquina não reclamada a ser desprovisionada e a incorrer em custos mínimos, os VMs podem ser deixados em laboratório por períodos de tempo mais longos.
- **Trabalhadores temporários/contratuais:** Permitir que os utilizadores utilizem uma máquina. Quando saem, devolvem o VM ao conjunto repreensível sem perda de dados. Com o VM não reclamado, outro utilizador pode reclamar o VM e continuar ou rever a máquina para obter informações adicionais.
- **Em geral:** A capacidade de ter uma única fonte configurar e implementar VMs automaticamente, numa cadência específica, é útil em muitas situações diferentes. Existem várias situações diferentes em que a funcionalidade de reclamação/não reclamação ajuda os utilizadores a serem mais eficientes, através de um processo automatizado para construir os VMs num estado não reclamado com uma configuração definida. A configuração pode incluir diferentes sistemas operativos, idiomas, discos ou [outros softwares (artefactos)](devtest-lab-artifact-author.md) dependendo das suas necessidades. A capacidade de reivindicar um VM do laboratório permite ao utilizador do laboratório obter um sistema devidamente configurado sem gastar o tempo ou esforço na configuração da máquina. O gerente do laboratório poderia usar o estado alegado dos VMs para melhorar o número de máquinas geradas, limpar máquinas e determinar a prioridade das configurações. A [fábrica de imagem](image-factory-create.md) é um bom exemplo de um processo automatizado para construir VMs e imagens para vários laboratórios. Os scripts podem ser modificados para executar qualquer uma das seguintes situações com as alterações apropriadas ou ser usados como referência para a criação de um sistema personalizado.

## <a name="situational-use-of-claimable-machines"></a>Utilização situacional de máquinas reempreensíveis

- Utilize a capacidade de reclamação/não-reclamação que permite aos utilizadores passar o controlo das máquinas de uma para outra e não ter de saber explicitamente quem irá apanhar a máquina a seguir.
- Desenvolvimento, teste e depuração de um cenário em que uma configuração específica da máquina pode reproduzir um bug, então a máquina pode ser não reclamada permitindo que outro desenvolvedor possa reclamar a máquina e continuar o trabalho. Esta funcionalidade é especialmente útil, uma vez que mais pessoas trabalham remotamente em diferentes áreas do mundo. 
- Os membros da equipa podem trabalhar com um único ambiente. Por exemplo, pode configurar manualmente um ambiente complexo que não pode ser automatizado ou criar recursos que só podem lidar com modificações para uma única entrada como imagens. No passado, este problema foi resolvido por ter uma máquina dedicada a funcionar. A funcionalidade reivível é uma melhoria sobre o processo manual, tendo o controlo integrado de acesso ao utilizador e identificação visual quando disponível. Quando não reclamado, o VM é desprovisionado para reduzir os custos.
- Tem um disco de dados ligado a um VM. Cada disco até ~ 1 TB de dados permite que um grande volume de dados seja passado sem ter que copiar ou duplicar os dados. O VM seria inicialmente criado com um disco anexo que tinha o grande volume de dados.  Qualquer utilizador poderia então reclamar a máquina e aceder aos dados. Quando feito, não reclame o VM para permitir que outros utilizadores possam chegar à máquina.

Existem algumas ressalvas em usar máquinas reiváveis, mais frequentemente em torno de ter acesso à máquina. Se a máquina for de domínio, então o utilizador alegando que a máquina terá de ter acesso já, normalmente é feito concedendo acesso a um grupo que engloba todos os utilizadores do laboratório quando o VM é criado. Se a máquina não for de domínio unida, o artefacto de **palavra-passe VM Reset** no repositório público terá de ser executado para adicionar o utilizador como administrador.  Os artefactos podem ser aplicados mesmo depois de a máquina ter sido iniciada ou reclamada.

## <a name="next-steps"></a>Passos seguintes
Ver o seguinte artigo: [Criar e gerir VMs reputáveis em Laboratórios Azure DevTest](devtest-lab-add-claimable-vm.md)
