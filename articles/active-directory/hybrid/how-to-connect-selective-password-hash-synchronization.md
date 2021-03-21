---
title: Sincronização seletiva de hash de palavra-passe para Azure AD Connect
description: Este artigo descreve como configurar e configurar a sincronização seletiva de hash de palavra-passe para usar com a Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/16/2021
ms.subservice: hybrid
ms.author: billmath
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 774c78cbb09d2e5e60dfc0cafc0082b25e9b1b45
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103603106"
---
# <a name="selective-password-hash-synchronization-configuration-for-azure-ad-connect"></a>Configuração seletiva de sincronização de hash de palavra-passe para Azure AD Connect

[A sincronização de hash de palavra-passe](whatis-phs.md) é um dos métodos de entrada usados para realizar a identidade híbrida. O Azure AD Connect sincroniza um haxixe, do haxixe, da palavra-passe de um utilizador de uma instância do Ative Directory no local para uma instância AD Azure baseada na nuvem.  Por predefinição, uma vez configurada, a sincronização de hash de palavra-passe ocorrerá em todos os utilizadores que está a sincronizar.

Se quiser ter um subconjunto de utilizadores excluídos da sincronização do hash da sua palavra-passe para Azure AD, pode configurar a sincronização seletiva de hash de palavra-passe utilizando os passos guiados fornecidos neste artigo.

>[!Important]
> A Microsoft não suporta modificar ou operar a sincronização do Azure AD Connect fora das configurações ou ações que estão formalmente documentadas. Qualquer uma destas configurações ou ações pode resultar num estado inconsistente ou não suportado da sincronização Azure AD Connect. Como resultado, a Microsoft não pode garantir que seremos capazes de fornecer suporte técnico eficiente para tais implementações. 


## <a name="consider-your-implementation"></a>Considere a sua implementação  
Para reduzir o esforço administrativo de configuração, deve primeiro considerar o número de objetos de utilizador que deseja excluir da sincronização de hash de palavra-passe. Verifique qual dos cenários abaixo, que são mutuamente exclusivos, alinha-se com os seus requisitos para selecionar a opção de configuração certa para si.
- Se o número de utilizadores a **excluir** for **menor** do que o número de utilizadores a **incluir,** siga os passos nesta [secção](#excluded-users-is-smaller-than-included-users).
- Se o número de utilizadores a **excluir** for **superior** ao número de utilizadores a **incluir,** siga os passos nesta [secção](#excluded-users-is-larger-than-included-users).

> [!Important]
> Com a opção de configuração escolhida, uma sincronização inicial necessária (Full Sync) para aplicar as alterações, será executada automaticamente durante o ciclo de sincronização seguinte.

### <a name="the-admindescription-attribute"></a>O atributo dedmindescrição
Ambos os cenários dependem da definição do atributo dedmindesscription dos utilizadores a um valor específico.  Isto permite que as regras sejam aplicadas e é o que faz o PHS seletivo funcionar.

|Scenario|valor dedmindesdedescrição|
|-----|-----|
|Os utilizadores excluídos são menores do que os utilizadores incluídos|PHSFiltered|
|Os utilizadores excluídos são maiores do que os utilizadores incluídos|PHSIncluded|

Este atributo pode ser definido:

- utilizando o Ative Directory Users and Computers UI
- utilizando `Set-ADUser` o cmdlet PowerShell.  Para mais informações consulte [Set-ADUser](https://docs.microsoft.com/powershell/module/addsadministration/set-aduser).

 


### <a name="disable-the-synchronization-scheduler"></a>Desative o programador de sincronização:
Antes de iniciar qualquer um dos cenários, tem de desativar o programador de sincronização enquanto faz alterações nas regras de sincronização.
 1. Iniciar janelas A PowerShell entra.

     ```Set-ADSyncScheduler -SyncCycleEnabled $false``` 
 
2.  Confirme que o programador está desativado executando o seguinte cmdlet:
     
    ```Get-ADSyncScheduler```

Para obter mais informações sobre o programador consulte o [programador de sincronização Azure AD Connect](how-to-connect-sync-feature-scheduler.md).




## <a name="excluded-users-is-smaller-than-included-users"></a>Os utilizadores excluídos são menores do que os utilizadores incluídos
A secção seguinte descreve como permitir a sincronização seletiva de haxixe de palavra-passe quando o número de utilizadores a **excluir** é **menor** do que o número de utilizadores a **incluir**.

>[!Important]
> Antes de proceder, certifique-se de que o programador de sincronização está desativado conforme descrito acima.

- Criar uma cópia editável **do In a partir de AD – User AccountEnabled** com a opção de **ativar a sincronização de haxixe de palavra-passe não selecionada** e definir o seu filtro de escotagem 
- Criar outra cópia editável do padrão **In a partir de AD – User AccountEnabled** com a opção de **ativar a sincronização de haxixe de palavra-passe selecionada** e definir o seu filtro de scoping 
- Re-ativar o programador de sincronização 
- Desafine o valor do atributo, no diretório ativo, que foi definido como atributo de scoping nos utilizadores que pretende permitir na sincronização de hash de palavra-passe. 

>[!Important]
>As etapas fornecidas para configurar a sincronização seletiva de hash de palavra-passe só afetarão objetos do utilizador que tenham o atributo **AdminDDDDdscription** povoado em Ative Directory com o valor de **PHSFiltered**.
Se este atributo não for povoado ou o valor for algo diferente do **PHSFiltered,** então estas regras não serão aplicadas aos objetos do utilizador.


### <a name="configure-the-necessary-synchronization-rules"></a>Configure as regras de sincronização necessárias:

 1. Inicie o Editor de Regras de Sincronização e desloque os filtros **Password Sync** para **On** and **Rule Type** para **Standard**.
     ![Iniciar editor de regras de sincronização](media/how-to-connect-selective-password-hash-synchronization/exclude-1.png)
 2. Selecione a regra **In a partir de AD – User AccountEnabled** para o Ative Directory forest Connector pretende configurar a palavra-passe seletiva com sincronização de hash e clique em **Editar**. Selecione **Sim** na caixa de diálogo seguinte para criar uma cópia editável da regra original.
     ![Selecione regra](media/how-to-connect-selective-password-hash-synchronization/exclude-2.png)
 3. A primeira regra irá desativar a sincronização de haxixe de palavra-passe. Fornecer o seguinte nome à nova regra personalizada: **In from AD - User AccountEnabled - Filter Users from PHS**.
 Altere o valor de precedência para um número inferior a 100 (por **exemplo, 90** ou o valor mais baixo disponível no seu ambiente).
 Certifique-se de que as caixas de verificação ativam o Sync e **o Desativado** da **Palavra-Passe** e c.
 Clique em **Seguinte**.
  ![Editar entrada](media/how-to-connect-selective-password-hash-synchronization/exclude-3.png)
 4. No **filtro Scoping,** clique na **cláusula Adicionar**.
 Selecione **adminDDDDDscription** na coluna atributo, **EQUAL** na coluna do operador e **introduza PHSFiltered** como o valor.
     ![Filtro de escotagem](media/how-to-connect-selective-password-hash-synchronization/exclude-4.png)
 5. Não são necessárias mais alterações. **As regras** de junção e transformações devem ser **deixadas** com as definições copiadas predefinidas para que possa clicar em **Guardar** agora.
 Clique **em OK** na caixa de diálogo de aviso informando que uma sincronização completa será executada no próximo ciclo de sincronização do conector.
     ![Salvar regra](media/how-to-connect-selective-password-hash-synchronization/exclude-5.png)
 6. Em seguida, crie outra regra personalizada com sincronização de hash de palavra-passe ativada. Selecione novamente a regra padrão **Ad – Conta de UtilizadorEnabled** para a floresta de Diretório Ativo pretende configurar a palavra-passe seletiva e clicar em **Editar**. Selecione **sim** na caixa de diálogo seguinte para criar uma cópia editável da regra original.
     ![Regra personalizada](media/how-to-connect-selective-password-hash-synchronization/exclude-6.png)
 7. Fornecer o seguinte nome à nova regra personalizada: **In from AD - User AccountEnabled - Utilizadores incluídos para PHS**.
 Altere o valor de precedência para um número inferior à regra anteriormente criada (neste exemplo serão **89).**
 Certifique-se de que a caixa de verificação **Enable Password Sync** é verificada e que a caixa de verificação **desativada** não é verificada.
 Clique em **Seguinte**.  
     ![Editar nova regra](media/how-to-connect-selective-password-hash-synchronization/exclude-7.png)
 8. No **filtro Scoping,** clique na **cláusula Adicionar**.
 Selecione **adminDDDDDscription** na coluna atributo, **NOTEQUAL** na coluna do Operador e **introduza PHSFiltered** como o valor.
     ![Regra de âmbito](media/how-to-connect-selective-password-hash-synchronization/exclude-8.png)
 9. Não são necessárias mais alterações. **As regras** de junção e transformações devem ser **deixadas** com as definições copiadas predefinidas para que possa clicar em **Guardar** agora.
 Clique **em OK** na caixa de diálogo de aviso informando que uma sincronização completa será executada no próximo ciclo de sincronização do conector.
     ![Aderir a regras](media/how-to-connect-selective-password-hash-synchronization/exclude-9.png)
 10. Confirme a criação de regras. Remover os filtros **Password Sync** **On** e **Rule Type** **Standard**. E devias ver as duas novas regras que acabaste de criar.
     ![Confirmar regras](media/how-to-connect-selective-password-hash-synchronization/exclude-10.png) 


### <a name="re-enable-synchronization-scheduler"></a>Re-activar o programador de sincronização:  
Uma vez concluídas as etapas para configurar as regras de sincronização necessárias, reepersificar o programador de sincronização com os seguintes passos:
 1. Na execução do Windows PowerShell:

     ```Set-ADSyncScheduler -SyncCycleEnabled $true```
 2. Em seguida, confirme que foi habilitado com sucesso executando:

     ```Get-ADSyncScheduler```

Para obter mais informações sobre o programador consulte o [programador de sincronização Azure AD Connect](how-to-connect-sync-feature-scheduler.md).

### <a name="edit-users-admindescription-attribute"></a>Editar atributo **dedmindscription:**
Uma vez concluídas todas as configurações, é necessário editar o **adminsscription** do atributo Para todos os utilizadores que deseje **excluir** da sincronização de hash de palavra-passe no Ative Directory e adicionar a cadeia utilizada no filtro de escotagem: **PHSFiltered**.
   
  ![Editar atributo](media/how-to-connect-selective-password-hash-synchronization/exclude-11.png)


## <a name="excluded-users-is-larger-than-included-users"></a>Os utilizadores excluídos são maiores do que os utilizadores incluídos
A secção seguinte descreve como permitir a sincronização seletiva de haxixe de palavra-passe quando o número de utilizadores a **excluir** é **maior** do que o número de utilizadores a **incluir**.

>[!Important]
> Antes de proceder, certifique-se de que o programador de sincronização está desativado conforme descrito acima.

Segue-se um resumo das ações que serão tomadas nos passos seguintes:

- Criar uma cópia editável **do In a partir de AD – User AccountEnabled** com a opção de **ativar a sincronização de haxixe de palavra-passe não selecionada** e definir o seu filtro de escotagem 
- Criar outra cópia editável do padrão **In a partir de AD – User AccountEnabled** com a opção de **ativar a sincronização de haxixe de palavra-passe selecionada** e definir o seu filtro de scoping 
- Re-ativar o programador de sincronização 
- Desafine o valor do atributo, no diretório ativo, que foi definido como atributo de scoping nos utilizadores que pretende permitir na sincronização de hash de palavra-passe. 

>[!Important]
>As etapas fornecidas para configurar a sincronização seletiva de hash de palavra-passe só afetarão objetos de utilizador que tenham o atributo **AdminDDDDdscription** povoado em Ative Directory com o valor de **PHSIncluded**.
Se este atributo não for povoado ou o valor for algo diferente de **PHSIncluded,** então estas regras não serão aplicadas aos objetos do utilizador.


### <a name="configure-the-necessary-synchronization-rules"></a>Configure as regras de sincronização necessárias:

 1. Inicie o Editor de Regras de Sincronização e desloque os filtros **Password Sync** **On** e **Rule Type** **Standard**.
     ![Tipo de regra](media/how-to-connect-selective-password-hash-synchronization/include-1.png)
 2. Selecione a regra **In a partir de AD – User AccountEnabled** para a floresta de Diretório Ativo que pretende configurar a palavra-passe seletiva tinha sincronização e clicar em **Editar**. Selecione **sim** na caixa de diálogo seguinte para criar uma cópia editável da regra original.
     ![Entrando da AD](media/how-to-connect-selective-password-hash-synchronization/include-2.png)
 3. A primeira regra irá desativar a sincronização de haxixe de palavra-passe. Fornecer o seguinte nome à nova regra personalizada: **In from AD - User AccountEnabled - Filter Users from PHS**.
 Altere o valor de precedência para um número inferior a 100 (por **exemplo, 90** ou o valor mais baixo disponível no seu ambiente).
 Certifique-se de que as caixas de verificação ativam o Sync e **o Desativado** da **Palavra-Passe.**
 Clique em **Seguinte**.
  ![Definir precedência](media/how-to-connect-selective-password-hash-synchronization/include-3.png)
 4. No **filtro Scoping,** clique na **cláusula Adicionar**.
Selecione **adminDDDDDscription** na coluna atributo, **NOTEQUAL** na coluna do Operador e introduza **PHSIncluded** como o valor.
     ![Adicionar cláusula](media/how-to-connect-selective-password-hash-synchronization/include-4.png)
 5. Não são necessárias mais alterações. **As regras** de junção e transformações devem ser **deixadas** com as definições copiadas predefinidas para que possa clicar em **Guardar** agora.
 Clique **em OK** na caixa de diálogo de aviso informando que uma sincronização completa será executada no próximo ciclo de sincronização do conector.
     ![Transformação](media/how-to-connect-selective-password-hash-synchronization/include-5.png)
 6. Em seguida, crie outra regra personalizada com sincronização de hash de palavra-passe ativada. Selecione novamente a regra padrão **Ad – Conta de UtilizadorEnabled** para a floresta de Diretório Ativo pretende configurar a palavra-passe seletiva e clicar em **Editar**. Selecione **sim** na caixa de diálogo seguinte para criar uma cópia editável da regra original.
     ![Conta de UtilizadorEnabled](media/how-to-connect-selective-password-hash-synchronization/include-6.png)
 7. Fornecer o seguinte nome à nova regra personalizada: **In from AD - User AccountEnabled - Utilizadores incluídos para PHS**.
 Altere o valor de precedência para um número inferior à regra anteriormente criada (neste exemplo serão **89).**
 Certifique-se de que a caixa de verificação **Enable Password Sync** é verificada e que a caixa de verificação **desativada** não é verificada.
 Clique em **Seguinte**.
     ![Ativar o Sincronização de Palavras-Passe](media/how-to-connect-selective-password-hash-synchronization/include-7.png)
 8. No **filtro Scoping,** clique na **cláusula Adicionar**.
 Selecione **adminDDDDDscription** na coluna atributo, **EQUAL** na coluna do operador e introduza **PHSIncluded** como o valor.
     ![PHSIncluded](media/how-to-connect-selective-password-hash-synchronization/include-8.png)
 9. Não são necessárias mais alterações. **As regras** de junção e transformações devem ser **deixadas** com as definições copiadas predefinidas para que possa clicar em **Guardar** agora.
 Clique **em OK** na caixa de diálogo de aviso informando que uma sincronização completa será executada no próximo ciclo de sincronização do conector.
     ![Poupe agora](media/how-to-connect-selective-password-hash-synchronization/include-9.png)
 10.    Confirme a criação de regras. Remover os filtros **Password Sync** **On** e **Rule Type** **Standard**. E devias ver as duas novas regras que acabaste de criar.
     ![Sincronização](media/how-to-connect-selective-password-hash-synchronization/include-10.png)

### <a name="re-enable-synchronization-scheduler"></a>Re-activar o programador de sincronização:  
Uma vez concluídas as etapas para configurar as regras de sincronização necessárias, reepersificar o programador de sincronização com os seguintes passos:
 1. Na execução do Windows PowerShell:

     ```Set-ADSyncScheduler -SyncCycleEnabled $true```
 2. Em seguida, confirme que foi habilitado com sucesso executando:

     ```Get-ADSyncScheduler```

Para obter mais informações sobre o programador consulte o [programador de sincronização Azure AD Connect](how-to-connect-sync-feature-scheduler.md).

### <a name="edit-users-admindescription-attribute"></a>Editar atributo **dedmindscription:**
Uma vez concluídas todas as configurações, é necessário editar o **admindscription** do atributo Para todos os utilizadores que deseje **incluir** para sincronização de hash de palavra-passe no Ative Directory e adicionar a cadeia utilizada no filtro de escotagem: **PHSIncluded**.

  ![Editar atributos](media/how-to-connect-selective-password-hash-synchronization/include-11.png)
 
 

## <a name="next-steps"></a>Passos Seguintes
- [O que é a sincronização de hash de palavra-passe?](whatis-phs.md)
- [Como funciona a sincronização de haxixe de palavra-passe](how-to-connect-password-hash-synchronization.md)
