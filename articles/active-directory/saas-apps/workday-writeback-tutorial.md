---
title: 'Tutorial: Configurar a gravação do Workday no Azure Ative Directory | Microsoft Docs'
description: Saiba como configurar a gravação do atributo Azure a Workday
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 10/14/2020
ms.author: chmutali
ms.openlocfilehash: 3a623a487dd31caf8c85b18771d90e3a6306df68
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954009"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-workday"></a>Tutorial: Configurar a gravação do atributo Azure AD para o Workday
O objetivo deste tutorial é mostrar os passos que precisa de executar para escrever atributos de Azure AD a Workday. A aplicação de writeback Workday suporta atribuir valores aos seguintes atributos workday:
* Email de Trabalho 
* Nome de utilizador do dia útil
* Número de telefone fixo de trabalho (incluindo código de país, código de área, número e extensão)
* Trabalho telefone fixo número principal bandeira
* Número de telemóvel de trabalho (incluindo código de país, código de área, número)
* Trabalhar bandeira primária móvel

## <a name="overview"></a>Descrição Geral

Depois de configurar a integração de provisionamento de entrada utilizando a aplicação de provisionamento [de AD no local](workday-inbound-tutorial.md) ou a aplicação de provisionamento do [Workday a Azure,](workday-inbound-cloud-only-tutorial.md) pode configurar opcionalmente a aplicação Workday Writeback para escrever informações de contacto, como e-mail de trabalho e número de telefone para Workday. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Para quem é a solução de provisionamento do utilizador mais adequada?

Esta solução de provisionamento do utilizador Workday Writeback é ideal para:

* Organizações que usam o Microsoft 365 que desejam escrever atributos autoritários geridos por TI (como endereço de e-mail, nome de utilizador e número de telefone) de volta ao Workday

## <a name="configure-integration-system-user-in-workday"></a>Configure o utilizador do sistema de integração no Workday

Consulte o utilizador do [sistema de integração configurar](workday-inbound-tutorial.md#configure-integration-system-user-in-workday) a secção para criar uma conta de utilizador do sistema de integração Workday com permissões para recuperar dados dos trabalhadores. 

## <a name="configuring-azure-ad-attribute-writeback-to-workday"></a>Configurar a Azure AD atribuir writee a Workday

Siga estas instruções para configurar a gravação dos endereços de e-mail do utilizador e do nome de utilizador do Azure Ative Directory para o Workday.

* [Adicionar a app do conector Writeback e criar a ligação ao Workday](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [Configurar mapeamentos de atributos de writeback](#part-2-configure-writeback-attribute-mappings)
* [Permitir e lançar o fornecimento de utilizadores](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>Parte 1: Adicionar a app do conector Writeback e criar a ligação ao Workday

**Para configurar o conector de writeback workday:**

1. Aceda a <https://portal.azure.com>.

2. No portal Azure, procure e selecione **O Diretório Ativo Azure**.

3. Selecione **Aplicações empresariais,** em seguida, **todas as aplicações**.

4. **Selecione Adicione uma aplicação** e, em seguida, selecione a categoria **'Todos'.**

5. Procure por **Workday Writeback** e adicione a aplicação na galeria.

6. Depois de adicionar a aplicação e mostrar o ecrã de detalhes da aplicação, selecione **Provisioning**.

7. Altere o **modo** **de provisionamento** para **Automático**.

8. Preencha a secção **credenciais de administração** da seguinte forma:

   * Nome de **utilizador Admin** – Introduza o nome de utilizador da conta do sistema de integração Workday, com o nome de domínio do inquilino anexado. Deve parecer algo como: *username \@ contoso4*

   * **Senha de administração –** Introduza a palavra-passe da conta do sistema de integração workday

   * **URL do inquilino –** Introduza o URL no ponto final dos serviços web workday para o seu inquilino. Este valor deve parecer: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources` , quando *o contoso4* é substituído pelo seu nome de inquilino correto e *o WD3-impl* é substituído pela cadeia ambiente correta (se necessário).

   * **E-mail de notificação –** Insira o seu endereço de e-mail e verifique a caixa de verificação "enviar e-mail se ocorrer falha".

   * Clique no botão **De Ligação de Teste.** Se o teste de ligação for bem sucedido, clique no botão **Guardar** na parte superior. Se falhar, verifique duas vezes se o URL do dia de trabalho e as credenciais são válidas no Workday.

### <a name="part-2-configure-writeback-attribute-mappings"></a>Parte 2: Configurar mapeamentos de atributos de writeback

Nesta secção, irá configurar como os atributos de writeback fluem de Azure AD para Workday. 

1. No separador Provisioning em **Mappings,** clique no nome do mapeamento.

2. No campo **Origem Object Scope,** pode filtrar opcionalmente, quais os conjuntos de utilizadores no Azure Ative Directory que devem fazer parte da gravação. O âmbito padrão é "todos os utilizadores em Azure AD".

3. Na secção **de mapeamentos do Atributo,** atualize o ID correspondente para indicar o atributo no Diretório Ativo Azure onde o ID do trabalhador do Workday ou iD do trabalhador está armazenado. Um método de correspondência popular é sincronizar o ID do trabalhador do Workday ou o ID do empregado para a extensãoAttribute1-15 em Azure AD, e, em seguida, usar este atributo em Azure AD para combinar os utilizadores de volta no Workday.

4. Normalmente mapeia o atributo Azure AD *userPrincipalName* ao atributo *Workday UserID* e mapeia o atributo *de correio* AD Azure para o atributo *Workday EmailAddress.* 

     >[!div class="mx-imgBorder"]
     >![Portal do Azure](./media/workday-inbound-tutorial/workday-writeback-mapping.png)

5. Utilize as orientações partilhadas abaixo para mapear os valores de atributos de Azure AD para Workday. 

     | Atributo de telefone de dia útil | Valor esperado | Orientação de mapeamento |
     |-------------------------|----------------|------------------|
     | WorkphoneLandlineIsPrimary | verdadeiro/falso | Mapeamento constante ou de expressão cuja saída é "verdadeiro" ou "falso" valor de cadeia. |
     | WorkphoneLandlineCountryCodeName | [Código de país ISO 3166-1 de três letras](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | Mapeamento constante ou de expressão cuja saída é um código de três letras do país. |
     | WorkphoneLandlineCountryCodeNumber | [Código de chamada do país internacional](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | Mapeamento constante ou de expressão cuja saída é um código de país válido (sem o sinal +). |
     | WorkphoneLandlineNumber | Número de telefone completo, incluindo o código de área | Mapa para *o telefone Atributo Número.* Utilize o regex para remover o whitespace, os suportes e o código do país. Veja o exemplo abaixo. |
     | WorkphoneLandlineExtension | Número de extensão | Se *o telefoneNumber contiver* extensão, utilize o Regex para extrair o valor. |
     | WorkphoneMobileIsPrimary | verdadeiro/falso | Mapeamento constante de mapeamento ou expressão cuja saída é "verdadeiro" ou "falso" valor de cadeia |
     | WorkphoneMobileCountryCodeName | [Código de país ISO 3166-1 de três letras](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | Mapeamento constante ou de expressão cuja saída é um código de três letras do país. |
     | WorkphoneMobileCountryCodeNumber | [Código de chamada do país internacional](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | Mapeamento constante ou de expressão cuja saída é um código de país válido (sem o sinal +). |
     | WorkphoneMobileNumber | Número de telefone completo, incluindo o código de área | Mapa para atributo *móvel.* Utilize o regex para remover o whitespace, os suportes e o código do país. Veja o exemplo abaixo. |

     > [!NOTE]
     > Ao invocar o serviço web Change_Work_Contact Workday, a Azure AD envia os seguintes valores constantes: <br>
     > * **Communication_Usage_Type_ID** está definido para a cadeia constante "WORK" <br>
     > * **Phone_Device_Type_ID** está definido para a cadeia constante "Mobile" para números de telemóvel e "Telefone Fixo" para números de telefone fixo. <br>
     > 
     > Você encontrará falhas de writeback se o seu inquilino workday usar diferentes Type_IDs. Para evitar tais falhas, pode utilizar a tarefa workday **Manter iDs** de referência e atualizar o Type_IDs para corresponder aos valores utilizados pelo Azure AD. <br>
     >  

     **Expressões regex de referência - Exemplo 1**

     Utilize a expressão abaixo, se o número de telefone em Azure AD for definido utilizando o formato necessário para o Reset de Senha de Autosserviço (SSPR). <br>
     Exemplo: se o valor do número de telefone for +1 1112223333 -> então a expressão regex irá produção 11122233333

     ```C#
     Replace([telephoneNumber], , "\\+(?<isdCode>\\d* )(?<phoneNumber>\\d{10})", , "${phoneNumber}", , )
     ```

     **Expressões regex de referência - Exemplo 2**

     Utilize a expressão abaixo, se o número de telefone em Azure AD estiver definido utilizando o formato (XXX) XXX-XXXX. <br>
     Exemplo: se o valor do número de telefone for (111) 222-3333 -> então a expressão regex irá produção 1112223333

     ```C#
     Replace([mobile], , "[()\\s-]+", , "", , )
     ```

6. Para guardar os seus mapeamentos, clique em **Guardar** na parte superior da secção Attribute-Mapping.

## <a name="enable-and-launch-user-provisioning"></a>Permitir e lançar o fornecimento de utilizadores

Uma vez concluídas as configurações da aplicação de provisionamento workday, pode ligar o serviço de prestação no portal Azure.

> [!TIP]
> Por padrão, quando ligar o serviço de fornecimento, iniciará operações de provisionamento para todos os utilizadores no âmbito. Se houver erros no mapeamento ou problemas de dados do Dia de Trabalho, então o trabalho de provisionamento pode falhar e entrar no estado de quarentena. Para evitar isto, como uma boa prática, recomendamos configurar o filtro **Source Object Scope** e testar os mapeamentos do seu atributo com alguns utilizadores de teste antes de lançar a sincronização completa para todos os utilizadores. Uma vez verificado que os mapeamentos funcionam e lhe estão a dar os resultados desejados, então pode remover o filtro ou expandi-lo gradualmente para incluir mais utilizadores.

1. No **separador Provisioning,** desa fixação do **Estado de Provisionamento** para **On**.

1. No **dropdown Scope,** **selecione Sync todos os utilizadores e grupos**. Com esta opção, a aplicação Writeback irá escrever atributos mapeados de todos os utilizadores de Azure AD a Workday, sujeitos às regras de deteção definidas no Âmbito do Objeto fonte **de Mapeamentos.**  ->   

   > [!div class="mx-imgBorder"]
   > ![Selecione o âmbito de writeback](./media/sap-successfactors-inbound-provisioning/select-writeback-scope.png)

   > [!NOTE]
   > A aplicação de provisionamento workday Writeback não suporta a opção **Sync apenas utilizadores e grupos atribuídos.**
 

2. Clique em **Guardar**.

3. Esta operação iniciará a sincronização inicial, que pode demorar um número variável de horas dependendo de quantos utilizadores estão no diretório de origem. Pode verificar a barra de progresso para acompanhar o progresso do ciclo de sincronização. 

4. A qualquer momento, consulte o **separador De provisionamento** no portal Azure para ver que ações o serviço de fornecimento executou. Os registos de auditoria listam todos os eventos sincronizados individuais realizados pelo serviço de fornecimento, tais como os utilizadores importados da fonte e exportados para a aplicação-alvo.  

5. Uma vez concluída a sincronização inicial, escreverá um relatório sumário no **separador Provisioning,** como mostrado abaixo.

     > [!div class="mx-imgBorder"]
     > ![Provisão de barras de progresso](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="known-issues-and-limitations"></a>Limitações e problemas conhecidos

* A aplicação Writeback utiliza um valor pré-definido para parâmetros **Communication_Usage_Type_ID** e **Phone_Device_Type_ID**. Se o seu inquilino workday estiver usando um valor diferente para estes atributos, então a operação Writeback não terá sucesso. Uma solução sugerida é atualizar o Type_IDs no Workday. 
* Quando a aplicação Writeback está configurada para atualizar os números de telefone secundários, não substitui o número de telefone secundário existente no Workday. Acrescenta mais um número de telefone secundário ao registo do trabalhador. Não há solução para este comportamento. 


## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre os cenários de integração do AZure AD e do Workday e as chamadas de serviço web](../app-provisioning/workday-integration-reference.md)
* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)
* [Saiba como configurar um único sign-on entre workday e Azure Ative Directory](workday-tutorial.md)
* [Saiba como integrar outras aplicações saaS com o Azure Ative Directory](tutorial-list.md)
* [Saiba como exportar e importar as suas configurações de provisionamento](../app-provisioning/export-import-provisioning-configuration.md)

