---
title: Como corrigir regras padrão modificadas - Azure AD Connect | Documentos da Microsoft
description: Saiba como corrigir as regras de predefinidas modificado que vêm com o Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.reviewer: darora10
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 761f3e6e72319a2e63d6b66f2893130ec5a82ebf
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698169"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Corrigir as regras predefinidas modificado no Azure AD Connect

O Azure AD Connect é fornecido com regras predefinidas para sincronização.  Infelizmente, essas regras não aplique universalmente para todas as organizações e pode haver ocasiões, com base nos seus requisitos, quando precisar de modificá-los.

 Se tiver modificado as regras predefinidas ou estiver a planear modificá-las, em seguida, dispense alguns momentos para ler este documento.

Este documento apresenta 2 exemplos das personalizações mais comuns feitas por usuários e explica a forma correta para alcançar essas personalizações.

>[!NOTE] 
> Modificação existente regra (s) predefinido para alcançar uma personalização necessária não é suportada - fazendo isso que impedirá a atualizar estas regras para a versão mais recente em versões futuras. Isto irá impedir que se necessárias correções de bugs e novos recursos.  Este documento explicar como alcançar o mesmo resultado sem modificar as regras predefinidas existente. 

## <a name="how-to-identify-modified-default-rules"></a>Como identificar regras predefinidas modificado?
A partir da versão 1.3.7.0 dos **do Azure AD Connect**, agora é fácil identificar a regra padrão modificadas. Pode aceder às aplicações no ambiente de trabalho e clique em **Editor de regras de sincronização**.

![Editor](media/how-to-connect-fix-default-rules/default1.png)

No Editor, quaisquer regras padrão modificadas serão apresentadas com um ícone na frente o nome como mostrado abaixo:

![Ícone](media/how-to-connect-fix-default-rules/default2.png)

 Verá também uma regra desativada com o mesmo nome junto ao mesmo, que é a regra predefinida padrão:

![Regras predefinidas](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>Personalizações comuns
Seguem-se as personalizações comuns para as regras predefinidas:

- [Alterar o fluxo de atributos](#changing-attribute-flow)
- [Alterar o filtro de âmbito](#changing-scoping-filter)
- [Alterar a condição de associação](#changing-join-condition)

## <a name="before-changing-any-rules"></a>Antes de alterar quaisquer regras
- Desative o agendador de sincronização.  O agendador é executado a cada 30 minutos por predefinição. Certifique-se de que não está a iniciar enquanto estiver a efetuar alterações e as novas regras de resolução de problemas. Para desativar temporariamente o scheduler, inicie o PowerShell e execute `Set-ADSyncScheduler -SyncCycleEnabled $false`.
 ![Regras predefinidas](media/how-to-connect-fix-default-rules/default3.png)

- A alteração no filtro de âmbito poderá resultar na eliminação de objetos no diretório de destino. Cuidado antes de fazer alterações no âmbito dos objetos. É recomendado para efetuar alterações a um servidor de teste antes de efetuar alterações no servidor do Active Directory.
- Execute uma pré-visualização num único objeto conforme mencionado na [validar a regra de sincronização](#validate-sync-rule) secção, depois de adicionar qualquer nova regra.
- Execute uma sincronização completa depois de adicionar uma nova regra ou modificar qualquer regra de sincronização personalizados. Esta sincronização aplicará novas regras a todos os objetos.

## <a name="changing-attribute-flow"></a>Alterar o fluxo de atributos
Existem 3 diferentes cenários para o fluxo de atributos, vamos ver como pode obtê-lo sem alterar as regras predefinidas padrão.
- Adicionar novo atributo
- Substituindo o valor do atributo existente
- Não sincroniza o atributo existente

### <a name="adding-new-attribute"></a>Adicionar novo atributo:
Se achar que um atributo não está a fluir do diretório de origem para o diretório de destino, em seguida, pode utilizar o [do Azure AD Connect: Extensões de diretório](how-to-connect-sync-feature-directory-extensions.md) para os novos atributos de fluxo.

Tenha em atenção que a sua primeira opção deve ser a utilização [do Azure AD Connect: Extensões de diretório](how-to-connect-sync-feature-directory-extensions.md), um fora do recurso de caixa fornecido pelo Azure AD Connect. No entanto, se não funcionar para em seguida, passar por passos seguintes para o fluxo de um atributo sem modificar a regra de sincronização de predefinido padrão existente, pode fazer isso adicionando duas novas regras de sincronização.


#### <a name="add-an-inbound-sync-rule"></a>Adicione uma regra de sincronização de entrada:
Regra de sincronização de entrada significa que a origem para o atributo é um espaço conector e de destino é metaverso. Por exemplo, a ser aplicada um novo atributo do Active Directory no local ao Azure Active Directory, criar uma nova regra de sincronização de entrada, iniciando a **Editor de regras de sincronização**, em seguida, selecione a direção como **entrada** e clique em **Adicionar nova regra**. 

 ![Regras predefinidas](media/how-to-connect-fix-default-rules/default3a.png)

Siga a sua própria Convenção de nomenclatura para nomear a regra, aqui usamos **personalizado do AD - utilizador**, isso significa que a regra é uma regra personalizada e uma regra de entrada do espaço de conector do AD para o Metaverso.   

 ![Regras predefinidas](media/how-to-connect-fix-default-rules/default3b.png)

Dê sua própria descrição da regra, para que a manutenção futura da regra é fácil, como o que é o objetivo desta regra e por que motivo foi necessária.
Selecione um ligado sistema (floresta) – a origem de atributo. Em seguida, selecione o tipo de objeto de sistema ligado e o tipo de objeto do Metaverso.

Especifique o valor de prioridade entre 0 – 99 (reduzir o número superior a precedência). Mantenha os outros campos, como "Tag", "Ativar a sincronização de palavra-passe" e "Disabled" como predefinição.

Mantenha 'Scoping filter' vazio, isso significa que a regra será aplicada a todos os objetos associados a um entre o sistema ligado do AD e de Metaverso.

Mantenha o vazio 'Regras de associação', que significa que esta regra irá na condição de associação definida na regra predefinido padrão. Esse é outro motivo para não desativar/eliminar a regra predefinida padrão porque se não houver nenhuma condição de associação pegar carona, em seguida, o atributo não fluirá. 

Adicionar transformação apropriada para seu atributo, pode atribuir constante para um valor constante para o atributo de destino do fluxo ou direcionar o mapeamento entre o atributo de origem ou destino ou uma expressão para o atributo. Seguem-se várias [funções de expressão](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference) pode utilizar.

#### <a name="add-an-outbound-sync-rule"></a>Adicione uma regra de sincronização de saída:
Até agora, adicionando apenas uma regra de sincronização de entrada que fizemos metade do trabalho, porque o atributo ainda não está ligado para o diretório de destino. Para ligar o atributo para o diretor de destino tem de criar uma regra de saída, o que significa que a origem é o metaverse e o destino for o sistema ligado. Para criar uma regra de saída, inicie **Editor de regras de sincronização**, altere a **direção** para **saída** e clique em **Adicionar nova regra**. 

![Regras predefinidas](media/how-to-connect-fix-default-rules/default3c.png)

Tal como a regra de entrada, pode usar sua própria Convenção de nomenclatura para **nome** a regra. Selecione o **sistema ligado** como inquilino do Azure AD, selecione o objeto de sistema ligado ao qual pretende definir o valor do atributo. Definir a precedência entre 0 - 99. 

![Regras predefinidas](media/how-to-connect-fix-default-rules/default3d.png)

Manter **filtro Scoping** vazio, keep **associar regras** vazio, preencha a transformação como constante, direta ou expressão. 

Neste exemplo demonstramos como fluxo novo atributo para um objeto de utilizador do Active Directory ao Azure Active Directory. Pode utilizar estes passos para mapear qualquer atributo de qualquer objeto de origem e destino.  Para obter mais informações, consulte [criação de regras de sincronização personalizados](how-to-connect-create-custom-sync-rule.md) e [preparar a aprovisionar utilizadores](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization).

### <a name="overriding-value-of-existing-attribute"></a>Substituindo o valor do atributo existente
É possível que pretende substituir o valor do atributo já mapeado, por exemplo sempre queira definir um valor nulo para um atributo no Azure AD, pode fazê-lo, simplesmente criando apenas uma regra de entrada conforme mencionado no passo anterior e o flow  **AuthoritativeNull** valor da constante para o atributo de destino. Tenha em atenção que usamos AuthoritativeNulll em vez de nulo neste caso. Isto acontece porque o valor não nulo substituirá o valor Null, mesmo que ele tenha precedência inferior (maior valor numérico na regra). No entanto, o AuthoritativeNull será tratado como Null e não será substituído pelo valor não nulo por outras regras. 

### <a name="dont-sync-existing-attribute"></a>Não sincroniza o atributo existente
Se quiser excluir um atributo da sincronização, em seguida, pode utilizar o atributo fornecido no Azure AD Connect de recurso de filtragem. Inicie **do Azure AD Connect** do ícone de área de trabalho e, em seguida, selecione **personalizar as opções de sincronização**.

![Regras predefinidas](media/how-to-connect-fix-default-rules/default4.png)

 Certifique-se **aplicação do Azure AD e filtragem de atributos** está selecionada e clique em **próxima**.

![Regras predefinidas](media/how-to-connect-fix-default-rules/default5.png)

Desmarque os atributos que pretende excluir da sincronização.

![Regras predefinidas](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="changing-scoping-filter"></a>Alterar o filtro de âmbito
O Azure AD Sync cuida da maior parte dos objetos, pode reduzir o âmbito dos objetos e reduzi-lo menos objetos para ser exportado numa forma suportada sem alterar as regras de sincronização predefinido padrão. Caso queira aumentar o escopo de objetos, em seguida, pode **editar** a regra existente, clone-o e desativar a regra original. A Microsoft recomenda que não aumente o âmbito configurado pelo Azure AD Connect. Aumento do âmbito de objetos, será mais difícil para a equipa de suporte compreender as personalizações e suporte do produto.

Aqui está como pode reduzir o âmbito dos objetos sincronizados com o Azure AD. Tenha em atenção que se reduzir o escopo a **utilizadores** a ser sincronizado a sincronização de hash de palavra-passe também será interrompida para os utilizadores filtrados-out. Se os objetos são já a sincronizar, em seguida, depois de reduzir o escopo, os objetos filtrados-out será eliminado do diretório de destino, trabalhe em conjunto no âmbito com muito cuidado.
Aqui estão as formas suportadas para reduzir o âmbito dos objetos que está a ser sincronizados.

- [atributo de cloudFiltered](#cloudfiltered-attribute)
- [Filtragem de UO](#ou-filtering)

### <a name="cloudfiltered-attribute"></a>atributo de cloudFiltered
Tenha em atenção que não se trata de um atributo que pode ser definido no Active Directory. Tem de definir o valor deste atributo, adicionando uma nova regra de entrada, conforme mencionado na **substituindo o valor do atributo existente** secção. Em seguida, pode utilizar o **transformação** e utilize **expressão** definir este atributo no Metaverso. Eis um exemplo que não pretende sincronizar todos do utilizador cujo nome de departamento começa com maiúsculas de minúsculas **HRD**:

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

Em primeiro lugar convertemos o departamento de origem (Active Directory) em minúsculas. Em seguida, usando a função à esquerda, podemos demorou apenas primeiros 3 carateres e comparado-lo com hrd. Se corresponder, defina o valor como nulo; caso contrário, VERDADEIRO. Tenha em atenção estiver a definir o valor NULL, para que pode escrever alguma outra regra com precedência inferior (valor de número mais alto) com a condição diferente. Execute um objeto para validar a regra de sincronização, conforme mencionado na pré-visualização [validar a regra de sincronização](#validate-sync-rule) secção.

![Regras predefinidas](media/how-to-connect-fix-default-rules/default7a.png)



### <a name="ou-filtering"></a>Filtragem de UO
Pode criar um ou mais UOs e mover os objetos que não pretende sincronizar para essas UOs. Em seguida, configure a UO filtragem no Azure AD Connect, iniciando **do Azure AD Connect** do ícone de área de trabalho e selecione as opções, conforme mostrado abaixo. Também pode configurar a UO filtragem no momento da instalação do Azure AD Connect. 

![Regras predefinidas](media/how-to-connect-fix-default-rules/default8.png)

Siga o assistente e, em seguida, anule a seleção de UOs que não pretende sincronizar.

![Regras predefinidas](media/how-to-connect-fix-default-rules/default9.png)

## <a name="changing-join-condition"></a>Alterar a condição de associação
A Microsoft recomenda que utilize a predefinição aderir ao condições configurados pelo Azure AD Connect. Mudanças nas condições de associação padrão serão mais difícil para a equipa de suporte compreender as personalizações e suporte do produto.

## <a name="validate-sync-rule"></a>Validar a regra de sincronização
Pode validar a regra de sincronização recentemente adicionado ao utilizar a funcionalidade de pré-visualização sem executar o ciclo de sincronização completa. Inicie **serviço de sincronização** interface do Usuário.

![Regras predefinidas](media/how-to-connect-fix-default-rules/default10.png)

Clique no **pesquisa de Metaverso**, selecione o objeto de âmbito conforme **pessoa**, **Adicionar cláusula** e mencionar os critérios de procura. Clique em **pesquisa** botão e faça duplo clique no objeto do **resultados da pesquisa** tenha em atenção que executar a importação e sincronização na floresta, antes de executar este passo, isto é garantir que os dados no Azure AD Connect está atualizado para esse objeto.

![Regras predefinidas](media/how-to-connect-fix-default-rules/default11.png)



Selecione **conectores**, selecione o objeto connector(forest) correspondente, clique em **propriedades...** .

![Regras predefinidas](media/how-to-connect-fix-default-rules/default12.png)

Clique no **pré-visualização...**

![Regras predefinidas](media/how-to-connect-fix-default-rules/default13a.png)

Clique em **gerar pré-visualização** e **fluxo de atributos de importação** no painel esquerdo.

![Regras predefinidas](media/how-to-connect-fix-default-rules/default14.png)
 
Aqui notará que a regra recentemente adicionada é executada no objeto e tem de definir o atributo de cloudFiltered como True.

![Regras predefinidas](media/how-to-connect-fix-default-rules/default15a.png)
 
Como comparar regra modificada com a regra predefinida?
Pode exportar as regras de ambos os separadamente como arquivos de texto. Estas regras serão exportadas como ficheiro de script do powershell. Pode compará-los usando qualquer ferramenta de comparação de arquivo para ver que tipo de alterações são feitas. Aqui neste exemplo, usei windiff para comparar dois arquivos.
 
Pode observar que no utilizador modificados regra, atributo msExchMailboxGuid é alterado para **expressão** em vez de **direto** com o valor como **nulo** e  **ExecuteOnce** opção. Pode ignorar as diferenças de Identified e precedência. 

![Regras predefinidas](media/how-to-connect-fix-default-rules/default17.png)
 
Como corrigir uma regra predefinida modificado?
Para corrigir as regras para as predefinições, pode eliminar a regra modificada e ativar a regra predefinida, conforme mostrado abaixo e, em seguida, executar uma **sincronização completa**. Antes de fazer tome medidas corretivas conforme mencionado acima, para que não perca a personalização que está tentando alcançar # # próximas etapas

## <a name="next-steps"></a>Próximos Passos
- [Hardware e pré-requisitos](how-to-connect-install-prerequisites.md) 
- [Definições rápidas](how-to-connect-install-express.md)
- [Definições personalizadas](how-to-connect-install-custom.md)

