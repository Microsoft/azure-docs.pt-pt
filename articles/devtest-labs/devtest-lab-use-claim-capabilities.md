---
title: Utilize capacidades de reivindicação em Azure DevTest Labs Microsoft Docs
description: Conheça diferentes cenários para utilizar capacidades de reivindicação/não-reclamação da Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 73ed3c0b94a66f5d17b5c8e2561c65bb48579aa1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85476534"
---
# <a name="use-claim-capabilities-in-azure-devtest-labs"></a>Utilize capacidades de reclamação em Azure DevTest Labs
O serviço Azure DevTest Labs melhora a eficácia e eficiência dos desenvolvedores e testadores. Este artigo centra-se na capacidade de reivindicar ou desclamar máquinas virtuais em Azure DevTest Labs. Também enumera várias formas de esta funcionalidade melhorar a experiência do utilizador. Antes de olhar para diferentes cenários onde esta funcionalidade pode ser usada, vamos olhar para o que é **reivindicar** e como funciona.

## <a name="claimable-machines"></a>Máquinas reclamantes
Uma máquina reivindicada é uma máquina virtual (VM) que é criada num laboratório sem dono. Uma vez reclamada a máquina, o utilizador tem uma gama completa de opções para esse VM. Quando um utilizador reclama uma máquina, são feitas algumas alterações. O VM é transferido da lista de **máquinas virtuais claimable** para a lista **my virtual machines** no portal Azure. 

O utilizador pode ligar-se ao VM, personalizar artefactos, reiniciar, parar ou não reclamar a máquina. Há algumas maneiras de tornar um VM reclamavel:

- Crie uma máquina e descoda-a para que se mova para a piscina reivindicada. 
- Crie um VM e coloque-o na piscina partilhada utilizando [configurações avançadas](https://azure.microsoft.com/updates/azure-devtest-labs-claim-lab-vms-from-a-shared-pool/).

Existem dois casos em que as capacidades de reclamação/não reclamação podem ser utilizadas de forma eficaz. O primeiro caso requer mais premeditação e planeamento, para ser projetado e executado corretamente. E a segunda é mais situacional. Seguem-se alguns exemplos dos diferentes casos.

## <a name="designed-use-of-claimable-machines"></a>Utilização projetada de máquinas reivindicaveis

- **Desenvolvimento / teste de software:** Permitir que os desenvolvedores ou testadores sejam mais produtivos por terem máquinas configuradas prontas e num estado não reclamado. Ter um conjunto de VMs com configurações diferentes, ferramentas necessárias, e com o código mais recente permite que os utilizadores reclamem um VM e comecem a trabalhar sem terem de gastar tempo para configurar uma máquina. Antes de os VM serem reclamados, as máquinas são a provisionadas, mas estão fechadas minimizando o custo de ter máquinas que são usadas com menos frequência. Quando os VM são necessários, um utilizador simplesmente afirma o VM, que liga a máquina. A opção não é tão útil neste caso, uma vez que a criação de um novo VM é muitas vezes mais fácil e barata.
- **Sala de aula/Laboratórios:** Ter VMs pré-configurados para uma aula ou laboratório para que os alunos possam ligar-se imediatamente a uma máquina usando o portal Azure.  Uma vez que um aluno reclame um VM, o laboratório garante que ninguém pode reclamar a mesma máquina. Automatizar este processo garante que o número necessário de máquinas com o ambiente especificado está disponível. Se os alunos não aparecerem ou se atrasarem, as máquinas não reclamadas podem ser mantidas disponíveis até que a sessão atérva com o custo mínimo. A opção não é tão eficaz neste cenário, uma vez que o VM está num estado desconhecido quando o utilizador anterior é feito.
- **Manifestações:** Use máquinas para demonstrações, onde as máquinas do laboratório são configuras com ambientes específicos. Esta capacidade é útil onde várias pessoas podem estar a fazer uma demonstração ao mesmo tempo ou em momentos aleatórios, como numa conferência. A opção não reclamada pode ser útil nesta situação, uma vez que a demonstração não deve alterar o estado da máquina, permitindo que os utilizadores devolvam um VM de volta à piscina reivindicada para a próxima demonstração. Com a máquina não reclamada a ser desprovisionada e a incorrer no custo mínimo, os VM podem ser deixados em laboratório por períodos de tempo mais longos.
- **Trabalhadores temporários/contratuais:** Permitir que os utilizadores utilizem uma máquina. Quando saem, devolvem o VM ao pool reclamatável sem perda de dados. Com o VM não reclamado, outro utilizador pode reclamar o VM e continuar ou rever a máquina para obter informações adicionais.
- **Em geral:** A capacidade de ter uma única fonte configurar e implantar VMs automaticamente, numa cadência específica, é útil em muitas situações diferentes. Existem várias situações diferentes em que a funcionalidade de reclamação/não reclamação ajuda os utilizadores a serem mais eficientes, tendo um processo automatizado para construir os VMs num estado não reclamado com uma configuração definida. A configuração(s) pode incluir diferentes sistemas operativos, idiomas, discos ou [outros softwares (artefactos)](devtest-lab-artifact-author.md) dependendo das suas necessidades. A capacidade de reivindicar um VM do laboratório permite ao utilizador do laboratório obter um sistema devidamente configurado sem gastar o tempo ou esforço na configuração da máquina. O gerente do laboratório poderia usar o estado reivindicado dos VMs para melhorar o número de máquinas geradas, limpar máquinas e determinar a prioridade das configurações. A [fábrica de Imagem](image-factory-create.md) é um bom exemplo de um processo automatizado para construir VMs e imagens para vários laboratórios. Os scripts podem ser modificados para executar qualquer uma das seguintes situações com as alterações apropriadas ou ser usados como referência para a criação de um sistema personalizado.

## <a name="situational-use-of-claimable-machines"></a>Utilização situacional de máquinas reclamantes

- Utilize a capacidade de reclamação/não reclamação que permite aos utilizadores passar o controlo das máquinas de uma para outra e não ter de saber explicitamente quem irá recolher a máquina a seguir.
- Desenvolvimento, teste e depurar um cenário em que uma configuração específica da máquina pode reproduzir um bug, então a máquina pode ser não reclamada permitindo que outro desenvolvedor possa reclamar a máquina e continuar o trabalho. Esta funcionalidade é especialmente útil, uma vez que mais pessoas trabalham remotamente em diferentes áreas do mundo. 
- Os membros da equipa podem trabalhar com um único ambiente. Por exemplo, pode configurar manualmente um ambiente complexo que não pode ser automatizado ou criar recursos que só podem lidar com modificações para uma única entrada como imagens. No passado, este problema foi resolvido por ter uma máquina dedicada a funcionar. A funcionalidade reivindicada é uma melhoria sobre o processo manual, tendo o controlo de acesso ao utilizador incorporado e a identificação visual quando disponível. Quando não reclamado, o VM é desprovisionado para reduzir custos.
- Tenha um disco de dados ligado a um VM. Cada disco até ~ 1 TB de dados permite que um grande volume de dados seja passado sem ter que copiar ou duplicar os dados. O VM seria inicialmente criado com um disco anexo que tivesse o grande volume de dados.  Qualquer utilizador poderia então reclamar a máquina e aceder aos dados. Quando terminar, não reclae o VM para permitir que outros utilizadores entrem na máquina.

Existem algumas ressalvas para o uso de máquinas reivindicaíveis, mais comummente em torno de ter acesso à máquina. Se a máquina for de domínio, então o utilizador alegando que a máquina já terá acesso, normalmente é feito através da concessão de acesso a um grupo que engloba todos os utilizadores em laboratório quando o VM é criado. Se a máquina não estiver unida ao domínio, o artefacto **reset VM Password** no repositório público terá de ser executado para adicionar o utilizador como administrador.  Os artefactos podem ser aplicados mesmo depois de a máquina ter sido iniciada ou reclamada.

## <a name="next-steps"></a>Passos seguintes
Ver o seguinte artigo: [Criar e gerir VMs reclamais em Azure DevTest Labs](devtest-lab-add-claimable-vm.md)
