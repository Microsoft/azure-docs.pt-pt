---
title: Como corrigir regras padrão modificadas - Azure AD Connect | Microsoft Docs
description: Saiba como corrigir regras padrão modificadas que vêm com Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.reviewer: darora10
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/21/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0fc1bc3158e04c9b1f677af7ef2375ac3ed2ce7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91320052"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Corrigir regras padrão modificadas no Azure AD Connect

Azure Ative Directory (Azure AD) Connect utiliza regras padrão para sincronização.  Infelizmente, estas regras não se aplicam universalmente a todas as organizações. Com base nos seus requisitos, talvez precise modificá-los. Este artigo discute dois exemplos das personalizações mais comuns e explica a forma correta de conseguir estas personalizações.

>[!NOTE] 
> Modificar as regras padrão existentes para obter uma personalização necessária não é suportado. Se o fizer, evita atualizar estas regras para a versão mais recente em futuras versões. Não conseguirá as correções de bug que precisa, ou novas funcionalidades. Este documento explica como obter o mesmo resultado sem modificar as regras de incumprimento existentes. 

## <a name="how-to-identify-modified-default-rules"></a>Como identificar regras padrão modificadas
Começando pela versão 1.3.7.0 do Azure AD Connect, é fácil identificar a regra padrão modificada. Vá a **Apps on Desktop** e selecione **Synchronization Rules Editor**.

![Azure AD Connect, com o Editor de Regras de Sincronização em destaque](media/how-to-connect-fix-default-rules/default1.png)

No Editor, todas as regras por defeito modificadas são apresentadas com um ícone de aviso na frente do nome.

![Ícone de aviso](media/how-to-connect-fix-default-rules/default2.png)

 Uma regra desativada com o mesmo nome ao lado também aparece (esta é a regra padrão padrão).

![Synchronization Rules Editor, mostrando regra padrão padrão e regra de incumprimento modificada](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>Personalizações comuns
Seguem-se as personalizações comuns às regras padrão:

- Alterar fluxo de atributos
- Mude o filtro de digitalização
- Mudança aderir condição

Antes de alterar quaisquer regras:

- Desative o programador de sincronização. O programador funciona a cada 30 minutos por defeito. Certifica-te de que não começa enquanto fazes alterações e que estás a resolver as tuas novas regras. Para desativar temporariamente o programador, inicie o PowerShell e corra `Set-ADSyncScheduler -SyncCycleEnabled $false` .
 ![Comandos PowerShell para desativar o programador de sincronização](media/how-to-connect-fix-default-rules/default3.png)

- A alteração do filtro de deteção pode resultar na eliminação de objetos no diretório alvo. Tenha cuidado antes de escoar quaisquer alterações na deteção de objetos. Recomendamos que esfadaremos as alterações num servidor de preparação antes de ese fazer alterações no servidor ativo.
- Executar uma pré-visualização num único objeto, como mencionado na secção Regra de [Sincronização Validada,](#validate-sync-rule) depois de adicionar qualquer nova regra.
- Executar uma sincronização completa depois de adicionar uma nova regra ou modificar qualquer regra de sincronização personalizada. Esta sincronização aplica novas regras a todos os objetos.

## <a name="change-attribute-flow"></a>Alterar fluxo de atributos
Existem três cenários diferentes para alterar o fluxo de atributos:
- Adicionando um novo atributo.
- Sobrevalorizar o valor de um atributo existente.
- Optando por não sincronizar um atributo existente.

Pode fazê-las sem alterar as regras padrão padrão.

### <a name="add-a-new-attribute"></a>Adicione um novo atributo
Se descobrir que um atributo não está a fluir do seu diretório de origem para o directório-alvo, utilize a [sincronização Azure AD Connect: Extensões](how-to-connect-sync-feature-directory-extensions.md) de diretório para corrigir isto.

Se as extensões não funcionarem para si, tente adicionar duas novas regras de sincronização, descritas nas seguintes secções.


#### <a name="add-an-inbound-sync-rule"></a>Adicione uma regra de sincronização de entrada
Uma regra de sincronização de entrada significa que a fonte para o atributo é um espaço de conector, e o alvo é o metaverso. Por exemplo, ter um novo fluxo de atributos do Ative Directory para o Azure Ative Directory, crie uma nova regra de sincronização de entrada. Lançar o **Editor de Regras de Sincronização,** selecione **Entrada** como direção e selecione Adicionar **nova regra**. 

 ![Screenshot que mostra o "Synchronization Rules Editor" com "Inbound" e "Add new rule" selecionados.](media/how-to-connect-fix-default-rules/default3a.png)

Siga a sua própria convenção de nomeação para nomear a regra. Aqui, utilizamos **o Custom In a partir de AD - Utilizador.** Isto significa que a regra é uma regra personalizada, e é uma regra de entrada do espaço do conector ative para o metaverso.   

 ![Criar regra de sincronização de entrada](media/how-to-connect-fix-default-rules/default3b.png)

Forneça a sua própria descrição da regra, para que a manutenção futura da regra seja fácil. Por exemplo, a descrição pode basear-se no que o objetivo da regra é, e por que é necessário.

Faça as suas seleções para os campos **Connected System**, Connected System **Object Type** e **Metaverse Object Type.**

Especificar o valor de precedência de 0 a 99 (quanto menor o número, maior a precedência). Para a **Tag**, Ative os campos **Desativação de Palavras-Passe** e **Desativados,** utilize as seleções predefinidoras.

Mantenha **o filtro de scoping** vazio. Isto significa que a regra se aplica a todos os objetos ligados entre o Sistema Ligado ao Diretório Ativo e o metaverso.

Mantenha **as regras de junção vazias.** Isto significa que esta regra utiliza a condição de junção definida na regra padrão padrão. Esta é mais uma razão para não desativar ou eliminar a regra padrão padrão. Se não houver nenhuma condição de união, o atributo não fluirá. 

Adicione transformações apropriadas para o seu atributo. Pode atribuir uma constante, para fazer um fluxo de valor constante para o seu atributo alvo. Pode utilizar o mapeamento direto entre o atributo de origem ou alvo. Ou pode usar uma expressão para o atributo. Aqui estão [várias funções de expressão](./reference-connect-sync-functions-reference.md) que pode usar.

#### <a name="add-an-outbound-sync-rule"></a>Adicione uma regra de sincronização de saída
Para ligar o atributo ao directório-alvo, é necessário criar uma regra de saída. Isto significa que a fonte é o metaverso, e o alvo é o sistema ligado. Para criar uma regra de saída, lance o **Editor de Regras de Sincronização,** altere a **Direção** para **Saída** e selecione Adicionar **nova regra**. 

![Editor de Regras de Sincronização](media/how-to-connect-fix-default-rules/default3c.png)

Tal como com a regra de entrada, podes usar a tua própria convenção de nomeação para nomear a regra. Selecione o **Sistema Conectado** como o inquilino AD Azure e selecione o objeto do sistema ligado ao qual pretende definir o valor do atributo. Desacorda a precedência de 0 a 99. 

![Criar regra de sincronização de saída](media/how-to-connect-fix-default-rules/default3d.png)

Mantenha **o filtro de scoping** e junte as **regras vazias.** Preencha a transformação como constante, direta ou expressão. 

Agora sabe como fazer um novo atributo para um objeto de utilizador fluir do Ative Directory para o Azure Ative Directory. Pode utilizar estes passos para mapear qualquer atributo de qualquer objeto para fonte e alvo. Para obter mais informações, consulte [criar regras de sincronização personalizadas](how-to-connect-create-custom-sync-rule.md) e [Prepare-se para obter utilizadores](/office365/enterprise/prepare-for-directory-synchronization).

### <a name="override-the-value-of-an-existing-attribute"></a>Sobrepor o valor de um atributo existente
Talvez queira anular o valor de um atributo que já foi mapeado. Por exemplo, se quiser sempre definir um valor nulo a um atributo em Ad Azure, basta criar uma regra de entrada. Faça o valor `AuthoritativeNull` constante, fluir para o atributo alvo. 

>[!NOTE] 
> Use `AuthoritativeNull` em vez de neste `Null` caso. Isto porque o valor não nulo substitui o valor nulo, mesmo que tenha uma precedência mais baixa (um valor numeral mais elevado na regra). `AuthoritativeNull`, por outro lado, não é substituído por um valor não nulo por outras regras. 

### <a name="dont-sync-existing-attribute"></a>Não sincronize o atributo existente
Se pretender excluir um atributo da sincronização, utilize a função de filtragem do atributo fornecida no Azure AD Connect. Lance **Azure AD Connect** a partir do ícone do ambiente de trabalho e, em seguida, selecione **Personalize as opções de sincronização**.

![Azure AD Conecte opções de tarefas adicionais](media/how-to-connect-fix-default-rules/default4.png)

 Certifique-se de que **a aplicação AD do AD e a filtragem do atributo** são selecionadas e selecione **Next**.

![Funcionalidades opcionais Azure AD Connect](media/how-to-connect-fix-default-rules/default5.png)

Limpe os atributos que pretende excluir da sincronização.

![Atributos AZURE AD Connect](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>Mude o filtro de digitalização
A Azure AD Sync cuida da maioria dos objetos. Pode reduzir o alcance dos objetos e reduzir o número de objetos a exportar, sem alterar as regras padrão de sincronização. 

Utilize um dos seguintes métodos para reduzir o âmbito dos objetos que está a sincronizar:

- atributo cloudFiltered
- Filtragem da unidade de organização

Se reduzir o âmbito de sincronização dos utilizadores, a sincronização de haxixe de palavra-passe também para para os utilizadores filtrados. Se os objetos já estiverem a sincronizar, depois de reduzir o alcance, os objetos filtrados são eliminados do diretório alvo. Por esta razão, certifique-se de que se trata com muito cuidado.

>[!IMPORTANT] 
> Aumentar o alcance dos objetos configurados pelo Azure AD Connect não é recomendado. Ao fazê-lo, é difícil para a equipa de suporte da Microsoft compreender as personalizações. Se tiver de aumentar o alcance dos objetos, edite a regra existente, clone-a e desative a regra original. 

### <a name="cloudfiltered-attribute"></a>atributo cloudFiltered
Não pode definir este atributo no Ative Directory. Desa estacie o valor deste atributo adicionando uma nova regra de entrada. Em seguida, pode utilizar **a Transformação** e **a Expressão** para definir este atributo no metaverso. O exemplo a seguir mostra que não pretende sincronizar todos os utilizadores cujo nome de departamento começa com **HRD** (caso-insensível):

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

Convertemos o departamento pela primeira vez de fonte (Ative Directory) para minúsculas. Depois, usando a `Left` função, pegamos apenas nos três primeiros caracteres e comparámo-lo `hrd` com. Se coincidir, o valor é definido `True` para, caso contrário `NULL` . Ao definir o valor para nulo, alguma outra regra com precedência mais baixa (um valor de número superior) pode escrever-lhe com uma condição diferente. Executar pré-visualização num objeto para validar a regra de sincronização, conforme mencionado na secção [regra de sincronização De Validação.](#validate-sync-rule)

![Criar opções de regras de sincronização de entrada](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>Filtragem da unidade organizacional
Pode criar uma ou mais unidades organizacionais (OUs) e mover os objetos que não quer sincronizar com estas OUs. Em seguida, configurar a filtragem ou em Azure AD Connect. Lance **Azure AD Connect** a partir do ícone do ambiente de trabalho e selecione as seguintes opções. Também pode configurar a filtragem da U no momento da instalação do Azure AD Connect. 

![Azure AD Conecte tarefas adicionais](media/how-to-connect-fix-default-rules/default8.png)

Siga o assistente e limpe as OUs que não quer sincronizar.

![Opções de filtragem Azure AD Connect Domain e OU](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>Mudança aderir condição
Utilize as condições de união predefinidas configuradas pelo Azure AD Connect. Alterar as condições de união por defeito dificulta o suporte da Microsoft a compreender as personalizações e a suportar o produto.

## <a name="validate-sync-rule"></a>Validar regra de sincronização
Pode validar a regra de sincronização recentemente adicionada utilizando a função de pré-visualização, sem executar todo o ciclo de sincronização. No Azure AD Connect, selecione **o Serviço de Sincronização**.

![Azure AD Connect, com Serviço de Sincronização em destaque](media/how-to-connect-fix-default-rules/default10.png)

Selecione **Metaverse Search**. Selecione o objeto de âmbito como **pessoa,** **selecione 'Adicionar Cláusula'** e mencione os seus critérios de pesquisa. Em seguida, **selecione Search** e clique duas vezes no objeto nos resultados da pesquisa. Certifique-se de que os seus dados no Azure AD Connect estão atualizados para esse objeto, executando a importação e sincronização na floresta antes de executar este passo.

![Synchronization Service Manager](media/how-to-connect-fix-default-rules/default11.png)

Nas **Propriedades de Objeto Metaverso,** selecione **Conectores,** selecione o objeto no conector correspondente (floresta) e selecione **Propriedades...**.

![Propriedades de objeto metaverso](media/how-to-connect-fix-default-rules/default12.png)

Selecione **Pré-visualização...**

![Propriedades de objetos de espaço do conector](media/how-to-connect-fix-default-rules/default13a.png)

Na janela pré-visualização, **selecione Gerar Pré-visualização** e **Import Attribute Flow** no painel esquerdo.

![Screenshot que mostra a janela "Preview" com "Import Attribute Flow" e "Generate Preview" selecionados.](media/how-to-connect-fix-default-rules/default14.png)
 
Aqui, note que a regra recém-adicionada é executada no objeto, e definiu o `cloudFiltered` atributo para verdadeiro.

![Pré-visualizar](media/how-to-connect-fix-default-rules/default15a.png)
 
Para comparar a regra modificada com a regra de incumprimento, exporte ambas as regras separadamente, como ficheiros de texto. Estas regras são exportadas como um ficheiro de script PowerShell. Pode compará-los utilizando qualquer ferramenta de comparação de ficheiros (por exemplo, windiff) para ver as alterações. 
 
Note que na regra modificada, o `msExchMailboxGuid` atributo é alterado para o tipo **expressão,** em vez de **Direct**. Além disso, o valor é alterado para opção **NULL** e **ExecuteOnce.** Pode ignorar as diferenças identificadas e de precedência. 

![saída de ferramenta windiff](media/how-to-connect-fix-default-rules/default17.png)
 
Para corrigir as suas regras para alterá-las de volta para as definições predefinidas, elimine a regra modificada e ative a regra por defeito. Certifique-se de que não perde a personalização que está a tentar alcançar. Quando estiver pronto, faça **a sincronização completa.**

## <a name="next-steps"></a>Passos seguintes
- [Hardware e pré-requisitos](how-to-connect-install-prerequisites.md) 
- [Definições expressas](how-to-connect-install-express.md)
- [Definições personalizadas](how-to-connect-install-custom.md)