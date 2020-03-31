---
title: Como corrigir regras de incumprimento modificadas - Azure AD Connect / Microsoft Docs
description: Aprenda a corrigir regras de padrão modificadas que vêm com o Azure AD Connect.
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
ms.openlocfilehash: 4626e0149028a140d143fb8d0969a03b732201fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79036982"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Corrigir regras de predefinição modificadas no Azure AD Connect

O Azure Ative Directory (Azure AD) Connect utiliza regras padrão para sincronização.  Infelizmente, estas regras não se aplicam universalmente a todas as organizações. Com base nos seus requisitos, pode ter de os modificar. Este artigo discute dois exemplos das personalizações mais comuns, e explica a forma correta de conseguir estas personalizações.

>[!NOTE] 
> Modificar as regras padrão existentes para alcançar uma personalização necessária não é suportado. Se o fizer, impede a atualização destas regras para a versão mais recente em futuras versões. Não terá as correções de bugs de que precisa, ou novas funcionalidades. Este documento explica como obter o mesmo resultado sem modificar as regras de incumprimento existentes. 

## <a name="how-to-identify-modified-default-rules"></a>Como identificar regras de incumprimento modificadas
Começando com a versão 1.3.7.0 do Azure AD Connect, é fácil identificar a regra de padrão modificada. Vá a **Apps no Desktop**e selecione **Synchronization Rules Editor**.

![Azure AD Connect, com Editor de Regras de Sincronização em destaque](media/how-to-connect-fix-default-rules/default1.png)

No Editor, quaisquer regras de predefinição modificadas são mostradas com um ícone de aviso na frente do nome.

![Ícone de aviso](media/how-to-connect-fix-default-rules/default2.png)

 Uma regra desativada com o mesmo nome ao lado também aparece (esta é a regra padrão padrão).

![Sincronização Regras Editor, mostrando regra padrão padrão e regra padrão modificada](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>Personalizações comuns
Seguem-se personalizações comuns às regras predefinidas:

- Alterar o fluxo de atributos
- Alterar o filtro de deteção
- Alterar condição de adesão

Antes de alterar quaisquer regras:

- Desative o programador de sincronização. O programador funciona a cada 30 minutos por defeito. Certifica-te de que não começa enquanto estás a fazer mudanças e a resolver problemas com as tuas novas regras. Para desativar temporariamente o programador, `Set-ADSyncScheduler -SyncCycleEnabled $false`inicie a PowerShell e corra .
 ![PowerShell ordena desativar o programador de sincronização](media/how-to-connect-fix-default-rules/default3.png)

- A alteração do filtro de deteção pode resultar na eliminação de objetos no directório-alvo. Tenha cuidado antes de efazer quaisquer alterações na deteção de objetos. Recomendamos que efaça alterações num servidor de encenação antes de efazer alterações no servidor ativo.
- Ecorra uma pré-visualização num único objeto, conforme mencionado na secção [Validate Sync Rule,](#validate-sync-rule) depois de adicionar qualquer nova regra.
- Executar uma sincronização completa depois de adicionar uma nova regra ou modificar qualquer regra de sincronização personalizada. Esta sincronização aplica novas regras a todos os objetos.

## <a name="change-attribute-flow"></a>Alterar o fluxo de atributos
Existem três cenários diferentes para alterar o fluxo de atributos:
- Adicionando um novo atributo.
- Sobrepondo o valor de um atributo existente.
- Optar por não sincronizar um atributo existente.

Pode fazê-lo sem alterar as regras padrão de incumprimento.

### <a name="add-a-new-attribute"></a>Adicione um novo atributo
Se descobrir que um atributo não está a fluir do seu diretório de origem para o directório-alvo, utilize a [sincronização Azure AD Connect: Extensões](how-to-connect-sync-feature-directory-extensions.md) de diretório para corrigir isto.

Se as extensões não funcionarem para si, tente adicionar duas novas regras de sincronização, descritas nas seguintes secções.


#### <a name="add-an-inbound-sync-rule"></a>Adicione uma regra de sincronização de entrada
Uma regra de sincronização de entrada significa que a fonte para o atributo é um espaço de conector, e o alvo é o metaverso. Por exemplo, ter um novo fluxo de atributos do Diretório Ativo no local para o Diretório Ativo Azure, criar uma nova regra de sincronização de entrada. Lançar o Editor de Regras de **Sincronização,** **selecione Inbound** como direção e selecione **Adicionar nova regra**. 

 ![Editor de Regras de Sincronização](media/how-to-connect-fix-default-rules/default3a.png)

Siga a sua própria convenção de nomeação para nomear a regra. Aqui, utilizamos **Custom In a partir de AD - Utilizador**. Isto significa que a regra é uma regra personalizada, e é uma regra de entrada do espaço do conector de diretório ativo ao metaverso.   

 ![Criar regra de sincronização de entrada](media/how-to-connect-fix-default-rules/default3b.png)

Forneça a sua própria descrição da regra, para que a manutenção futura da regra seja fácil. Por exemplo, a descrição pode basear-se no que é o objetivo da regra, e por que é necessário.

Faça as suas seleções para os campos de **objetos** **conectados,** do sistema conectado e do **tipo de objeto metaverso.**

Especifique o valor de precedência de 0 a 99 (quanto menor o número, maior a precedência). Para a **etiqueta**, ative o **Sincronização de Passwords**e os campos **dedesactivação,** utilize as seleções predefinidas.

Mantenha **o filtro de deteção** vazio. Isto significa que a regra se aplica a todos os objetos unidos entre o Sistema Ligado ao Diretório Ativo e o metaverso.

Mantenha **as regras de unir** vazias. Isto significa que esta regra utiliza a condição de união definida na regra padrão padrão. Esta é outra razão para não desativar ou eliminar a regra padrão de incumprimento. Se não houver condição de união, o atributo não fluirá. 

Adicione transformações apropriadas para o seu atributo. Pode atribuir uma constante, para fazer um fluxo de valor constante para o seu atributo-alvo. Pode utilizar o mapeamento direto entre a fonte ou o atributo-alvo. Ou pode usar uma expressão para o atributo. Aqui estão várias [funções](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference) de expressão que pode usar.

#### <a name="add-an-outbound-sync-rule"></a>Adicione uma regra de sincronização de saída
Para ligar o atributo ao diretório alvo, precisa criar uma regra de saída. Isto significa que a fonte é o metaverso, e o alvo é o sistema conectado. Para criar uma regra de saída, lance o Editor de Regras de **Sincronização,** mude a **Direção** para **Outbound**e selecione **Adicionar nova regra**. 

![Editor de Regras de Sincronização](media/how-to-connect-fix-default-rules/default3c.png)

Tal como a regra de entrada, podeusar a sua própria convenção de nomeação para nomear a regra. Selecione o **Sistema Conectado** como inquilino DaD Azure e selecione o objeto de sistema conectado ao qual pretende definir o valor do atributo. Desmarcar a precedência de 0 a 99. 

![Criar regra de sincronização de saída](media/how-to-connect-fix-default-rules/default3d.png)

Mantenha **o filtro de deteção** e junte as **regras** vazias. Preencha a transformação como constante, direta ou expressão. 

Agora sabe como fazer um novo atributo para um fluxo de objeto de utilizador do Ative Directory para o Azure Ative Directory. Pode utilizar estes passos para mapear qualquer atributo de qualquer objeto para fonte e alvo. Para mais informações, consulte Criar regras de [sincronização personalizadas](how-to-connect-create-custom-sync-rule.md) e [preparar-se para fornecer aos utilizadores](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization).

### <a name="override-the-value-of-an-existing-attribute"></a>Anular o valor de um atributo existente
Talvez queira anular o valor de um atributo que já foi mapeado. Por exemplo, se quiser sempre definir um valor nulo a um atributo em Azure AD, basta criar uma regra de entrada apenas. Faça o valor `AuthoritativeNull`constante, flua para o atributo alvo. 

>[!NOTE] 
> Use `AuthoritativeNull` em `Null` vez de neste caso. Isto porque o valor não nulo substitui o valor nulo, mesmo que tenha precedência mais baixa (um valor de número mais elevado na regra). `AuthoritativeNull`Por outro lado, não é substituído por um valor não nulo por outras regras. 

### <a name="dont-sync-existing-attribute"></a>Não sincronize o atributo existente
Se pretender excluir um atributo da sincronização, utilize a função de filtragem do atributo fornecida no Azure AD Connect. Lance **o Azure AD Connect** a partir do ícone do ambiente de trabalho e, em seguida, selecione Personalizar opções de **sincronização**.

![Opções adicionais de tarefas do Azure AD Connect](media/how-to-connect-fix-default-rules/default4.png)

 Certifique-se de que a **aplicação Azure AD e a filtragem** do atributo são selecionadas e selecione **Next**.

![Funcionalidades opcionais azure AD Connect](media/how-to-connect-fix-default-rules/default5.png)

Limpe os atributos que pretende excluir da sincronização.

![Ad Connect Azure AD](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>Alterar o filtro de deteção
O Azure AD Sync cuida da maioria dos objetos. Pode reduzir o âmbito dos objetos e reduzir o número de objetos a exportar, sem alterar as regras padrão de sincronização padrão. 

Utilize um dos seguintes métodos para reduzir o alcance dos objetos que está a sincronizar:

- atributo filtrado em nuvem
- Filtragem da unidade de organização

Se reduzir o âmbito de sincronização dos utilizadores, a sincronização de hash de palavra-passe também para para os utilizadores filtrados. Se os objetos já estiverem a sincronizar, depois de reduzir o alcance, os objetos filtrados são eliminados do directório-alvo. Por esta razão, certifique-se de que procura com muita atenção.

>[!IMPORTANT] 
> Não é aconselhável aumentar o âmbito dos objetos configurados pelo Azure AD Connect. Ao fazê-lo, dificulta que a equipa de suporte da Microsoft compreenda as personalizações. Se tiver de aumentar o âmbito dos objetos, editar a regra existente, cloná-la e desativar a regra original. 

### <a name="cloudfiltered-attribute"></a>atributo filtrado em nuvem
Não pode definir este atributo no Diretório Ativo. Desestabeleça o valor deste atributo adicionando uma nova regra de entrada. Em seguida, pode utilizar **a Transformação** e **a Expressão** para definir este atributo no metaverso. O exemplo que se segue mostra que não quer sincronizar todos os utilizadores cujo nome de departamento começa com **HRD** (caso-insensível):

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

Primeiro convertemos o departamento de fonte (Diretório Ativo) para minúscula. Depois, usando `Left` a função, pegamos apenas nos `hrd`três primeiros caracteres e comparamos com . Se coincidir, o valor é `True`definido `NULL`para, caso contrário. Ao definir o valor a nulo, alguma outra regra com precedência mais baixa (um valor de número mais elevado) pode escrever-lhe com uma condição diferente. Executar a pré-visualização de um objeto para validar a regra de sincronização, conforme mencionado na secção de regra de [sincronização validada.](#validate-sync-rule)

![Criar opções de regra de sincronização de entrada](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>Filtragem de unidade organizacional
Pode criar uma ou mais unidades organizacionais (OUs) e mover os objetos que não quer sincronizar com estas OUs. Em seguida, configure a filtragem ou em Azure AD Connect. Lance **o Azure AD Connect** a partir do ícone do ambiente de trabalho e selecione as seguintes opções. Também pode configurar a filtragem ou no momento da instalação do Azure AD Connect. 

![Azure AD Connect tarefas adicionais](media/how-to-connect-fix-default-rules/default8.png)

Siga o assistente e limpe as OUs que não quer sincronizar.

![Opções de conectão Azure AD e filtragem de OU](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>Alterar condição de adesão
Utilize as condições de adesão predefinidas configuradas pelo Azure AD Connect. Alterar as condições de adesão padrão dificulta que o suporte da Microsoft compreenda as personalizações e suporte o produto.

## <a name="validate-sync-rule"></a>Validar regra de sincronização
Pode validar a regra de sincronização recentemente adicionada utilizando a função de pré-visualização, sem executar o ciclo de sincronização completo. No Azure AD Connect, selecione **Serviço de Sincronização**.

![Azure AD Connect, com serviço de sincronização em destaque](media/how-to-connect-fix-default-rules/default10.png)

Selecione **Procura metaversa**. Selecione o objeto de âmbito como **pessoa,** selecione **Adicionar Cláusula,** e mencione os seus critérios de pesquisa. Em seguida, selecione **Search**e clique duas vezes no objeto nos resultados da pesquisa. Certifique-se de que os seus dados no Azure AD Connect estão atualizados para esse objeto, executando a importação e sincronização na floresta antes de executar este passo.

![Synchronization Service Manager](media/how-to-connect-fix-default-rules/default11.png)

Em **Propriedades de Objetos Metaversos,** selecione **Conectores,** selecione o objeto no conector correspondente (floresta) e selecione **Propriedades...**.

![Propriedades de objetos metaversos](media/how-to-connect-fix-default-rules/default12.png)

Selecione **Pré-visualização...**

![Propriedades de objetos espaciais conector](media/how-to-connect-fix-default-rules/default13a.png)

Na janela Preview, **selecione Generate Preview** e Import **Attribute Flow** no painel esquerdo.

![Pré-visualização](media/how-to-connect-fix-default-rules/default14.png)
 
Aqui, note que a regra recém-adicionada é `cloudFiltered` executada sobre o objeto, e estabeleceu o atributo como verdadeiro.

![Pré-visualização](media/how-to-connect-fix-default-rules/default15a.png)
 
Para comparar a regra modificada com a regra de incumprimento, exporte ambas as regras separadamente, como ficheiros de texto. Estas regras são exportadas como um ficheiro de script PowerShell. Pode compará-las utilizando qualquer ferramenta de comparação de ficheiros (por exemplo, windiff) para ver as alterações. 
 
Note que na regra `msExchMailboxGuid` modificada, o atributo é alterado para o tipo **expressão,** em vez de **Direct**. Além disso, o valor é alterado para a opção **NULL** e **ExecuteOnce.** Pode ignorar as diferenças identificadas e precedência. 

![saída de ferramenta windiff](media/how-to-connect-fix-default-rules/default17.png)
 
Para corrigir as suas regras para as alterar de volta às definições predefinidas, elimine a regra modificada e ative a regra predefinida. Certifique-se de que não perde a personalização que está a tentar alcançar. Quando estiver pronto, faça **a Sincronização Completa.**

## <a name="next-steps"></a>Passos seguintes
- [Hardware e pré-requisitos](how-to-connect-install-prerequisites.md) 
- [Configurações expressas](how-to-connect-install-express.md)
- [Definições personalizadas](how-to-connect-install-custom.md)



