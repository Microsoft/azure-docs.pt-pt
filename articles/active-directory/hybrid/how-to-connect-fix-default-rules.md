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
ms.openlocfilehash: d2f0956b44d6df64fb73e5eee7844574237d8755
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067640"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Corrigir as regras predefinidas modificado no Azure AD Connect

O Azure Active Directory (Azure AD) Connect utiliza regras predefinidas para a sincronização.  Infelizmente, essas regras não se aplicam universalmente para todas as organizações. Com base nos seus requisitos, poderá ter de modificá-los. Este artigo descreve dois exemplos das personalizações mais comuns e explica a forma correta para alcançar essas personalizações.

>[!NOTE] 
> Modificar regras de predefinidas existentes para alcançar uma personalização necessária não é suportada. Se o fizer, portanto, ele impede a atualizar estas regras para a versão mais recente em futuras versões. Não obtém as correções de erros que precisa, ou novas funcionalidades. Este documento explica como alcançar o mesmo resultado sem modificar as regras predefinidas existente. 

## <a name="how-to-identify-modified-default-rules"></a>Como identificar regras predefinidas modificado
A partir da versão 1.3.7.0 do Azure AD Connect, é fácil identificar a regra padrão modificadas. Aceda a **aplicações no ambiente de trabalho**e selecione **Editor de regras de sincronização**.

![O Azure AD Connect, com o Editor de regras de sincronização realçado](media/how-to-connect-fix-default-rules/default1.png)

No Editor, quaisquer regras padrão modificadas são apresentadas com um ícone de aviso na frente do nome.

![Ícone de aviso](media/how-to-connect-fix-default-rules/default2.png)

 Um desativado da regra com o mesmo nome junto a é também apresentado (esta é a regra predefinida padrão).

![Editor de regras de sincronização, que mostra a regra predefinida padrão e a regra predefinida modificado](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>Personalizações comuns
Seguem-se as personalizações comuns para as regras predefinidas:

- Alterar o fluxo de atributos
- Alterar o filtro de âmbito
- Condição de associação de alteração

Antes de alterar quaisquer regras:

- Desative o agendador de sincronização. O agendador é executado a cada 30 minutos por predefinição. Certifique-se de que não está a iniciar enquanto estiver a fazer alterações e as novas regras de resolução de problemas. Para desativar temporariamente o scheduler, inicie o PowerShell e execute `Set-ADSyncScheduler -SyncCycleEnabled $false`.
 ![Comandos do PowerShell para desativar o agendador de sincronização](media/how-to-connect-fix-default-rules/default3.png)

- A alteração no filtro de âmbito pode resultar na eliminação de objetos no diretório de destino. Tenha cuidado antes de fazer alterações no âmbito dos objetos. Recomendamos que fizer alterações a um servidor intermediário antes de efetuar alterações no servidor do Active Directory.
- Executar uma pré-visualização num único objeto, conforme mencionado na [validar a regra de sincronização](#validate-sync-rule) secção, depois de adicionar qualquer nova regra.
- Após adicionar uma nova regra ou modificar qualquer regra de sincronização personalizados, execute uma sincronização completa. Esta sincronização aplica-se novas regras a todos os objetos.

## <a name="change-attribute-flow"></a>Alterar o fluxo de atributos
Existem três cenários diferentes para alterar o fluxo de atributos:
- Adicionar um novo atributo.
- Substituindo o valor de um atributo existente.
- Escolha não sincronizar um atributo existente.

Pode fazer isso sem alterar as regras predefinidas padrão.

### <a name="add-a-new-attribute"></a>Adicionar um novo atributo
Se achar que um atributo não é a fluir do diretório de origem para o diretório de destino, utilize o [do Azure AD Connect: Extensões de diretório](how-to-connect-sync-feature-directory-extensions.md) para corrigir este problema.

Se as extensões não funcionarem para, tente adicionar duas regras de sincronização novo, descritas nas seções a seguir.


#### <a name="add-an-inbound-sync-rule"></a>Adicionar uma regra de sincronização de entrada
Uma regra de sincronização de entrada significa que a origem para o atributo é um espaço conector e o destino é o metaverse. Por exemplo, ter um novo atributo fluxo a partir do local do Active Directory ao Azure Active Directory, criar uma nova regra de sincronização de entrada. Iniciar o **Editor de regras de sincronização**, selecione **entrada** como a direção e selecione **Adicionar nova regra**. 

 ! Sincronização regras Editor](media/how-to-connect-fix-default-rules/default3a.png)

Siga a sua própria Convenção de nomenclatura para nomear a regra. Aqui, podemos usar **personalizada do AD - utilizador**. Isso significa que a regra é uma regra personalizada e é uma regra de entrada do espaço conector do Active Directory para o metaverso.   

 ![Criar regra de sincronização de entrada](media/how-to-connect-fix-default-rules/default3b.png)

Fornece sua própria descrição da regra, para que a manutenção futura da regra é fácil. Por exemplo, pode basear-se a descrição sobre o que é o objetivo da regra e, por que motivo é necessária.

Faça as suas seleções os **sistema ligado**, **tipo de objeto de sistema ligado**, e **tipo de objeto Metaverso** campos.

Especifique o valor de precedência de 0 a 99 (menor o número, maior será a precedência). Para o **Tag**, **ativar a sincronização de palavra-passe**, e **desativado** campos, utilize as seleções predefinidas.

Manter **Scoping filtro** vazio. Isso significa que a regra se aplica a todos os objetos associados entre o sistema de ligado de diretório Active Directory e o metaverse.

Manter **associar regras** vazio. Isso significa que esta regra utiliza a condição de associação definida na regra predefinido padrão. Esse é outro motivo para não desativar ou eliminar a regra predefinida padrão. Se não houver nenhuma condição de associação, o atributo não fluir. 

Adicione transformações apropriadas para seu atributo. Pode atribuir uma constante, para tornar uma constante de valor de fluxo para o atributo de destino. Pode usar o mapeamento direto entre o atributo de origem ou destino. Em alternativa, pode utilizar uma expressão para o atributo. Seguem-se várias [funções de expressão](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference) pode utilizar.

#### <a name="add-an-outbound-sync-rule"></a>Adicionar uma regra de sincronização de saída
Para ligar o atributo para o diretório de destino, terá de criar uma regra de saída. Isso significa que a origem é o metaverse e o destino é o sistema ligado. Para criar uma regra de saída, inicie o **Editor de regras de sincronização**, altere a **direção** para **saída**e selecione **Adicionar nova regra**. 

![Editor de regras de sincronização](media/how-to-connect-fix-default-rules/default3c.png)

Como com a regra de entrada, pode usar sua própria Convenção de nomenclatura para nomear a regra. Selecione o **sistema ligado** como o inquilino do Azure AD e selecione o sistema ligado o objeto ao qual pretende define o valor do atributo. Defina a precedência de 0 a 99. 

![Criar regra de sincronização de saída](media/how-to-connect-fix-default-rules/default3d.png)

Manter **filtro Scoping** e **associar regras** vazio. Preencha a transformação como constante, direta ou expressão. 

Agora, sabe como fazer um novo atributo para um fluxo de objeto de utilizador do Active Directory ao Azure Active Directory. Pode utilizar estes passos para mapear qualquer atributo de qualquer objeto de origem e destino. Para obter mais informações, consulte [criação de regras de sincronização personalizados](how-to-connect-create-custom-sync-rule.md) e [preparar a aprovisionar utilizadores](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization).

### <a name="override-the-value-of-an-existing-attribute"></a>Substituir o valor de um atributo existente
Convém substituir o valor de um atributo que já foi mapeado. Por exemplo, se desejar sempre definir um valor nulo para um atributo no Azure AD, basta crie uma regra de entrada apenas. Faça o valor constante, `AuthoritativeNull`, flow para o atributo de destino. 

>[!NOTE] 
> Uso `AuthoritativeNull` em vez de `Null` neste caso. Isto acontece porque o valor não nulo substitui o valor nulo, mesmo que ela tenha precedência inferior (um valor mais alto número na regra). `AuthoritativeNull`, por outro lado, não é, por outras regras, substituídos por um valor não nulo. 

### <a name="dont-sync-existing-attribute"></a>Não sincroniza o atributo existente
Se quiser excluir um atributo da sincronização, use o atributo de filtragem de recurso fornecido no Azure AD Connect. Inicie **do Azure AD Connect** do ícone de área de trabalho e, em seguida, selecione **personalizar as opções de sincronização**.

![Opções de tarefas adicionais do Azure AD Connect](media/how-to-connect-fix-default-rules/default4.png)

 Certifique-se **aplicação do Azure AD e filtragem de atributos** está selecionado e selecione **próxima**.

![Funcionalidades de opcionais do Azure AD Connect](media/how-to-connect-fix-default-rules/default5.png)

Desmarque os atributos que pretende excluir da sincronização.

![Atributos do Azure AD Connect](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>Alterar o filtro de âmbito
O Azure AD Sync se encarrega da maior parte dos objetos. Pode reduzir o âmbito dos objetos e reduzir o número de objetos para ser exportada, sem alterar as regras de sincronização predefinido padrão. 

Utilize um dos seguintes métodos para reduzir o âmbito dos objetos que se estiver a sincronizar:

- atributo de cloudFiltered
- Filtragem de unidade de organização

Se reduzir o âmbito de utilizadores que está a ser sincronizados, a sincronização de hash de palavra-passe será também interrompido o para os utilizadores filtrados-out. Se os objetos já estão sincronizados, depois de reduzir o escopo, os objetos filtrados-out são eliminados do diretório de destino. Por esse motivo, certifique-se de que definir o âmbito com muito cuidado.

>[!IMPORTANT] 
> Aumentar o âmbito dos objetos configurado pelo Azure AD Connect não é recomendado. Se o fizer, torna difícil para a equipa de suporte da Microsoft compreender as personalizações. Se tem de aumentar o escopo de objetos, editar a regra existente, clone-o e desativar a regra original. 

### <a name="cloudfiltered-attribute"></a>atributo de cloudFiltered
Não é possível definir este atributo no Active Directory. Defina o valor deste atributo, adicionando uma nova regra de entrada. Em seguida, pode utilizar **transformação** e **expressão** definir este atributo no metaverso. O exemplo seguinte mostra que não pretende sincronizar todos os usuários cujo nome de departamento começa pela **HRD** (maiúsculas de minúsculas):

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

Vamos primeiro convertido o departamento de origem (Active Directory) em minúsculas. Em seguida, utilizar o `Left` função, demorou apenas os primeiros três carateres e comparados com `hrd`. Se correspondente, o valor é definido como `True`, caso contrário `NULL`. Na definição do valor como nulo, alguma outra regra com precedência inferior (um valor mais alto número) pode escrever no mesmo com uma condição diferente. Pré-visualização execução um objeto para validar a regra de sincronização, conforme mencionado na [regra de sincronização de validar](#validate-sync-rule) secção.

![Criar opções de regra de sincronização de entrada](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>Filtragem de unidade organizacional
Pode criar um ou mais unidades organizacionais (UOs) e mover os objetos que não pretende sincronizar para essas UOs. Em seguida, configure a UO filtragem no Azure AD Connect. Inicie **do Azure AD Connect** do ícone de área de trabalho e selecione as seguintes opções. Também pode configurar a UO filtragem no momento da instalação do Azure AD Connect. 

![Tarefas de adicionais do Azure AD Connect](media/how-to-connect-fix-default-rules/default8.png)

Siga o assistente e desmarque as UOs que não pretende sincronizar.

![Azure AD Connect Domain e opções de filtragem de UO](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>Condição de associação de alteração
Utilize as condições de associação padrão configuradas pelo Azure AD Connect. Mudanças nas condições de associação padrão torna difícil para o suporte da Microsoft para compreender as personalizações e suporte do produto.

## <a name="validate-sync-rule"></a>Validar a regra de sincronização
Pode validar a regra de sincronização recentemente adicionado ao utilizar a funcionalidade de pré-visualização, sem executar o ciclo de sincronização completa. No Azure AD Connect, selecione **serviço de sincronização**.

![O Azure AD Connect, com o serviço de sincronização realçado](media/how-to-connect-fix-default-rules/default10.png)

Selecione **pesquisa de Metaverso**. Selecione o objeto de âmbito como **pessoa**, selecione **Adicionar cláusula**e mencionar os critérios de procura. Em seguida, selecione **pesquisa**e clique duas vezes o objeto nos resultados da pesquisa. Certifique-se de que os seus dados no Azure AD Connect estão atualizados para esse objeto, ao executar a importação e sincronização na floresta, antes de executar este passo.

![Synchronization Service Manager](media/how-to-connect-fix-default-rules/default11.png)

No **propriedades de objeto Metaverso**, selecione **conectores**, selecione o objeto no conector do correspondente (floresta) e selecione **propriedades...** .

![Propriedades do objeto de Metaverso](media/how-to-connect-fix-default-rules/default12.png)

Selecione **pré-visualização...**

![Propriedades de objeto de espaço conector](media/how-to-connect-fix-default-rules/default13a.png)

Na janela de pré-visualização, selecione **gerar pré-visualização** e **fluxo de atributos de importação** no painel esquerdo.

![Pré-visualização](media/how-to-connect-fix-default-rules/default14.png)
 
Aqui, tenha em atenção que a regra recentemente adicionada é executada no objeto e definiu o `cloudFiltered` atributo como true.

![Pré-visualização](media/how-to-connect-fix-default-rules/default15a.png)
 
Para comparar a regra modificada com a regra predefinida, exporte ambas as regras em separado, como arquivos de texto. Estas regras são exportadas como um ficheiro de script do PowerShell. Pode compará-los usando qualquer ferramenta de comparação de ficheiro (por exemplo, o windiff) para ver as alterações. 
 
Tenha em atenção que a regra modificada, o `msExchMailboxGuid` atributo é alterado para o **expressão** tipo, em vez de **direto**. Além disso, o valor é alterado para **nulo** e **ExecuteOnce** opção. Pode ignorar as diferenças de Identified e precedência. 

![resultado da ferramenta Windiff](media/how-to-connect-fix-default-rules/default17.png)
 
Para resolver suas regras para alterá-los novamente para as predefinições, eliminar a regra modificada e ativar a regra predefinida. Certifique-se de que não perde a personalização que está tentando alcançar. Quando estiver pronto, execute **sincronização completa**.

## <a name="next-steps"></a>Passos Seguintes
- [Hardware e pré-requisitos](how-to-connect-install-prerequisites.md) 
- [Definições rápidas](how-to-connect-install-express.md)
- [Definições personalizadas](how-to-connect-install-custom.md)



