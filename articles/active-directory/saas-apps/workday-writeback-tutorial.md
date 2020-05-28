---
title: 'Tutorial: Configure Workday writeback in Azure Ative Directory [ Diretório Ativo] Microsoft Docs'
description: Saiba como configurar a atribuição de reprodução de Azure AD para o Dia de Trabalho
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: ad255bd4-9e50-43a1-a92b-359215867b6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/26/2020
ms.author: chmutali
ms.openlocfilehash: 1d76fb96676ad49ce28ff4ef0d6c4fbc84636638
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84041793"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-workday"></a>Tutorial: Configure atribua a devolução de Azure AD ao Dia de Trabalho
O objetivo deste tutorial é mostrar os passos que precisa de dar para reescrever atributos de Azure AD para Workday. A aplicação workday writeback suporta a atribuição de valores aos seguintes atributos do Workday:
* Email de trabalho 
* Nome de utilizador do dia de trabalho
* Número de telefone fixo de trabalho (incluindo código de país, código de área, número e extensão)
* Trabalho telefone fixo bandeira primária
* Número de telemóvel de trabalho (incluindo código de país, código de área, número)
* Trabalho bandeira primária móvel

## <a name="overview"></a>Descrição geral

Depois de configurar a integração de fornecimento de entrada utilizando o Workday para a aplicação de provisionamento [de AD no local](workday-inbound-tutorial.md) ou para a aplicação de provisionamento de [AD Azure,](workday-inbound-cloud-only-tutorial.md) pode configurar opcionalmente a app Workday Writeback para escrever informações de contacto, como email de trabalho e número de telefone para o Workday. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Para quem é esta solução de fornecimento de utilizadores mais adequada?

Esta solução de fornecimento de utilizadores Workday Writeback é ideal para:

* Organizações que usam o Office 365 que desejam reescrever atributos autoritários geridos por TI (como endereço de e-mail, nome de utilizador e número de telefone) de volta ao Dia do Trabalho

## <a name="configure-integration-system-user-in-workday"></a>Configure o utilizador do sistema de integração no Dia do Trabalho

Consulte a secção configurar o utilizador do sistema de [integração](workday-inbound-tutorial.md#configure-integration-system-user-in-workday) para criar uma conta de utilizador do sistema de integração Workday com permissões para recuperar dados dos trabalhadores. 

## <a name="configuring-azure-ad-attribute-writeback-to-workday"></a>Configurar a AD Azure atribui a reprodução ao Dia do Trabalho

Siga estas instruções para configurar a redação dos endereços de e-mail do utilizador e do nome de utilizador do Diretório Ativo Azure para o Dia do Trabalho.

* [Adicionar a aplicação de conector Writeback e criar a ligação ao Dia do Trabalho](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [Configure mapeamento sinuoso de atributos](#part-2-configure-writeback-attribute-mappings)
* [Ativar e lançar o fornecimento de utilizadores](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>Parte 1: Adicionar a aplicação de conector Writeback e criar a ligação ao Dia do Trabalho

**Para configurar o conector Workday Writeback:**

1. Aceda a <https://portal.azure.com>.

2. No portal Azure, procure e selecione **Azure Ative Directory**.

3. Selecione **Aplicações Empresariais,** em **seguida, todas as aplicações**.

4. **Selecione Adicionar uma aplicação**e, em seguida, selecione a categoria **All.**

5. Procure **workday Writeback**e adicione essa aplicação na galeria.

6. Depois da adição da aplicação e do ecrã de detalhes da aplicação, selecione **Provisioning**.

7. Mude o **modo** **de provisionamento** para **automático**.

8. Complete a secção **de Credenciais de Administrador** da seguinte forma:

   * **Nome** de utilizador do Administrador – Introduza o nome de utilizador da conta do sistema de integração Workday, com o nome de domínio do arrendatário anexado. Deve parecer algo como: *username \@ contoso4*

   * **Senha de administração –** Insira a palavra-passe da conta do sistema de integração do Dia de Trabalho

   * **URL do inquilino –** Insira o URL no ponto final dos serviços web workday para o seu inquilino. Este valor deve parecer: , onde o `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources` *contoso4* é substituído pelo seu nome de inquilino correto e *wd3-impl* é substituído pela corda ambiental correta (se necessário).

   * **Email de notificação –** Insira o seu endereço de e-mail e verifique a caixa de verificação "enviar e-mail se ocorrer falha".

   * Clique no botão **de ligação** de teste. Se o teste de ligação for bem sucedido, clique no botão **Guardar** na parte superior. Se falhar, verifique duas vezes se o URL do Dia de Trabalho e as credenciais são válidas no Dia do Trabalho.

### <a name="part-2-configure-writeback-attribute-mappings"></a>Parte 2: Configurar mapeamentos de atributos de redação

Nesta secção, irá configurar como os atributos de redação fluem de Azure AD para Workday. 

1. No separador Provisioning em **Mapeamentos,** clique no nome do mapeamento.

2. No campo **Source Object Scope,** pode filtrar opcionalmente, que conjuntos de utilizadores no Diretório Ativo Azure devem fazer parte da reversão. O âmbito padrão é "todos os utilizadores em Azure AD".

3. Na secção de **mapeamento** sacar, atualize o ID correspondente para indicar o atributo no Diretório Ativo Azure onde o ID do trabalhador do Workday ou identificação do empregado é armazenado. Um método de correspondência popular é sincronizar o ID ou id do trabalhador do trabalhador do workday para extensãoAttribute1-15 em Azure AD, e, em seguida, usar este atributo em Azure AD para combinar os utilizadores no Dia de Trabalho.

4. Normalmente, mapeia o atributo do utilizador Azure AD *PrincipalName* ao atributo do *UserID* do Dia de Trabalho e mapeie o atributo de *correio* AD Azure ao atributo do Workday *EmailAddress.* 

     >[!div class="mx-imgBorder"]
     >![Portal do Azure](./media/workday-inbound-tutorial/workday-writeback-mapping.png)

5. Utilize a orientação partilhada abaixo para mapear valores de atributos de número de telefone de Azure AD para O Dia de Trabalho. 

     | Atributo de telefone de dia de trabalho | Valor esperado | Orientação de mapeamento |
     |-------------------------|----------------|------------------|
     | WorkphoneLandlineIsPrimary | verdadeiro/falso | Mapeamento constante ou de expressão cuja saída é "verdadeira" ou "falsa" de valor de corda. |
     | WorkphoneLandlineCountryCodeName | [Código de país ISO 3166-1 de três letras](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | Mapeamento constante ou de expressão cuja saída é um código de três letras do país. |
     | WorkphoneLandlineCountryCodeNumber | [Código de chamada de país internacional](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | Mapeamento constante ou de expressão cuja saída é um código de país válido (sem o sinal +). |
     | WorkphoneLandlineNumber | Número de telefone completo, incluindo o código de área | Mapa para *telefoneNumber* atributo. Utilize o regex para remover o espaço branco, os suportes e o código do país. Veja o exemplo abaixo. |
     | Extensão workphoneLandline | Número de extensão | Se *o telefoneNumber* contiver extensão, utilize o regex para extrair o valor. |
     | WorkphoneMobileIsPrimary | verdadeiro/falso | Mapeamento constante ou mapeamento de expressão cuja saída é "verdadeiro" ou "falso" valor de corda |
     | WorkphoneMobileCountryCodeName | [Código de país ISO 3166-1 de três letras](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | Mapeamento constante ou de expressão cuja saída é um código de três letras do país. |
     | WorkphoneMobileCountryCodeNumber | [Código de chamada de país internacional](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | Mapeamento constante ou de expressão cuja saída é um código de país válido (sem o sinal +). |
     | WorkphoneMobileNumber | Número de telefone completo, incluindo o código de área | Mapa para atributo *móvel.* Utilize o regex para remover o espaço branco, os suportes e o código do país. Veja o exemplo abaixo. |

     > [!NOTE]
     > Ao invocar o serviço web Change_Work_Contact Workday, a Azure AD envia os seguintes valores constantes: <br>
     > * **Communication_Usage_Type_ID** está definido para a cadeia constante "WORK" <br>
     > * **Phone_Device_Type_ID** está definida para constante string "Mobile" para números de telemóvel e "Telefone Fixo" para números de telefone fixo. <br>
     > 
     > Você encontrará falhas de redação se o seu inquilino do Workday usar diferentes Type_IDs. Para evitar tais falhas, pode utilizar a tarefa workday **Keep Reference IDs** e atualizar o Type_IDs para corresponder aos valores utilizados pela Azure AD. <br>
     >  

     **Expressãos regex de referência - Exemplo 1**

     Utilize a expressão regular abaixo, se o número de telefone em Azure AD for definido utilizando o formato necessário para redefinir a palavra-passe self service (SSPR). <br>
     Exemplo: se o valor do número de telefone for +1 111222333 -> então a expressão regex irá de 11122233333

     ```C#
     Replace([telephoneNumber], , "\\+(?<isdCode>\\d* )(?<phoneNumber>\\d{10})", , "${phoneNumber}", , )
     ```

     **Expressãos regex de referência - Exemplo 2**

     Utilize a expressão regular abaixo, se o número de telefone em Azure AD for definido utilizando o formato (XXX) XXX-XXXX. <br>
     Exemplo: se o valor do número de telefone for (111) 222-3333 -> então a expressão regex irá ser de 1112223333

     ```C#
     Replace([mobile], , "[()\\s-]+", , "", , )
     ```

6. Para guardar os seus mapeamentos, clique em **Guardar** no topo da secção Attribute-Mapping.

## <a name="enable-and-launch-user-provisioning"></a>Ativar e lançar o fornecimento de utilizadores

Uma vez concluídas as configurações da aplicação de provisionamento Workday, pode ligar o serviço de provisionamento no portal Azure.

> [!TIP]
> Por predefinição, quando ligar o serviço de provisionamento, iniciará operações de provisionamento para todos os utilizadores no âmbito. Se houver erros nas questões de mapeamento ou dados do Dia do Trabalho, então o trabalho de provisionamento pode falhar e ir para o estado de quarentena. Para evitar isto, como uma boa prática, recomendamos configurar o filtro **Source Object Scope** e testar os seus mapeamentos de atributos com alguns utilizadores de teste antes de lançar a sincronização completa para todos os utilizadores. Uma vez verificado que os mapeamentos funcionam e estão a dar-lhe os resultados desejados, então pode remover o filtro ou expandi-lo gradualmente para incluir mais utilizadores.

1. No separador **provisionamento,** coloque o estado de **provisionamento** **ligado**.

2. Clique em **Guardar**.

3. Esta operação iniciará a sincronização inicial, que pode demorar um número variável de horas dependendo de quantos utilizadores estão no diretório de origem. Pode verificar a barra de progresso para acompanhar o progresso do ciclo de sincronização. 

4. A qualquer momento, verifique o separador de **registos de auditoria** no portal Azure para ver que ações o serviço de provisionamento realizou. Os registos de auditoria listam todos os eventos de sincronização individuais realizados pelo serviço de provisionamento, tais como os utilizadores são importados da fonte e exportados para a aplicação-alvo.  

5. Uma vez concluída a sincronização inicial, escreverá um relatório sumário no separador **Provisioning,** como mostrado abaixo.

     > [!div class="mx-imgBorder"]
     > ![Fornecimento de barra de progresso](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Próximos passos

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
* [Saiba como configurar um único sign-on entre o Workday e o Azure Ative Directory](workday-tutorial.md)
* [Saiba como integrar outras aplicações do SaaS com o Diretório Ativo Azure](tutorial-list.md)
* [Saiba como exportar e importar as suas configurações de provisionamento](../app-provisioning/export-import-provisioning-configuration.md)

