---
title: 'Azure AD Connect: Ativar a repetição de escrita do dispositivo | Documentos da Microsoft'
description: Este documento fornece detalhes sobre como ativar a repetição de escrita do dispositivo com o Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: femila
editor: curtand
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7ed6fd0a4a1de3fb02b3d8583c0e5c0cecac211
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60351841"
---
# <a name="azure-ad-connect-enabling-device-writeback"></a>Azure AD Connect: Ativar a repetição de escrita do dispositivo
> [!NOTE]
> Uma subscrição do Azure AD Premium é necessária para repetição de escrita do dispositivo.
> 
> 

A seguinte documentação fornece informações sobre como ativar a funcionalidade de repetição de escrita do dispositivo no Azure AD Connect. Repetição de escrita do dispositivo é utilizada nos seguintes cenários:

* Ativar o acesso condicional com base em dispositivos para ADFS (2012 R2 ou superior) a aplicações (confianças de entidades confiadoras) protegidas.

Esta opção fornece segurança adicional e o controle de acesso a aplicações é concedido apenas aos dispositivos fidedignos. Para obter mais informações sobre o acesso condicional, consulte [gestão de riscos com acesso condicional](../active-directory-conditional-access-azure-portal.md) e [configuração de acesso condicional no local com o registo de dispositivos do Azure Active Directory](../../active-directory/active-directory-device-registration-on-premises-setup.md).

> [!IMPORTANT]
> <li>Dispositivos têm de estar localizados na mesma floresta que os utilizadores. Uma vez que os dispositivos têm de ser escritos novamente para uma única floresta, esta funcionalidade não suporta atualmente uma implementação com várias florestas de utilizador.</li>
> <li>Objeto de configuração do dispositivo apenas um registo pode ser adicionado à floresta do Active Directory no local. Esta funcionalidade não é compatível com uma topologia em que o Active Directory no local está sincronizado com vários diretórios do Azure AD.</li>

## <a name="part-1-install-azure-ad-connect"></a>Parte 1: Instalar o Azure AD Connect
Instalar o Azure AD Connect com personalizado ou definições rápidas. A Microsoft recomenda começar com todos os utilizadores e grupos sincronizados com êxito antes de ativar a repetição de escrita do dispositivo.

## <a name="part-2-enable-device-writeback-in-azure-ad-connect"></a>Parte 2: Ativar a repetição de escrita do dispositivo no Azure AD Connect
1. Execute novamente o Assistente de instalação. Selecione **configurar opções do dispositivo** página de tarefas adicionais e clique em **próxima**. 

    ![Configurar opções do dispositivo](./media/how-to-connect-device-writeback/deviceoptions.png)

    >[!NOTE]
    > As novas opções de dispositivo de configurar está disponível apenas na versão 1.1.819.0 e mais recente.

2. Na página de opções do dispositivo, selecione **configurar a repetição de escrita do dispositivo**. A opção de **desativar a repetição de escrita do dispositivo** não estarão disponíveis até que seja ativada a repetição de escrita do dispositivo. Clique em **seguinte** para mover para a página seguinte do assistente.
    ![Optou por operação de dispositivo](./media/how-to-connect-device-writeback/configuredevicewriteback1.png)

3. Na página de repetição de escrita, verá o domínio fornecido como a floresta de repetição de escrita do dispositivo predefinido.
   ![Personalizado floresta de destino de repetição de escrita a dispositivos de instalação](./media/how-to-connect-device-writeback/writebackforest.png)

4. **Contentor de dispositivo** página fornece a opção de preparação do active directory, utilizando uma das duas opções disponíveis:

    a. **Fornecer credenciais de administrador de empresa**: Se as credenciais de administrador de empresa são fornecidas para a floresta em que dispositivos têm de repetição de escrita, o Azure AD Connect irá preparar a floresta automaticamente durante a configuração da repetição de escrita do dispositivo.

    b. **Transferir o script do PowerShell**: O Azure AD Connect gera automaticamente um script do PowerShell que pode preparar o active directory para repetição de escrita do dispositivo. No caso de não não possível fornecer as credenciais de administrador de empresa no Azure AD Connect, recomenda-se para transferir o script do PowerShell. Fornecer o script do PowerShell transferido **CreateDeviceContainer.psq** para o administrador de empresa da floresta em que dispositivos serão repetição de escrita.
    ![Preparar a floresta do Active Directory](./media/how-to-connect-device-writeback/devicecontainercreds.png)
    
    As seguintes operações são executadas para preparar a floresta do active directory:
    * Se não existirem já, cria e configura os novos contentores e objetos em CN = Configuration de registo do dispositivo, CN = Services, CN = Configuration, [floresta-dn].
    * Se não existirem já, cria e configura os novos contentores e objetos em CN = RegisteredDevices, [domínio-dn]. Objetos de dispositivo serão criados neste contentor.
    * Define as permissões necessárias na conta do conector do Azure AD, para gerir dispositivos no seu Active Directory.
    * Só precisa ser executado numa floresta, mesmo que o Azure AD Connect está a ser instalado em várias florestas.

## <a name="verify-devices-are-synchronized-to-active-directory"></a>Certifique-se de que dispositivos são sincronizados com o Active Directory
Repetição de escrita do dispositivo deve agora estar a funcionar corretamente. Lembre-se de que pode demorar até 3 horas para objetos de dispositivo ser a repetição de escrita para o AD.  Para verificar que os dispositivos estão a ser sincronizados corretamente, faça o seguinte depois de concluir as regras de sincronização:

1. Inicie o Centro de administração do Active Directory.
2. Expanda RegisteredDevices, dentro do domínio que está a ser federado.

   ![Dispositivos registados do Centro de administração do Active Directory](./media/how-to-connect-device-writeback/devicewriteback5.png)

3. Dispositivos registados atuais serão listados aqui.

   ![Centro de administração do Active Directory registado a lista de dispositivos](./media/how-to-connect-device-writeback/devicewriteback6.png)

## <a name="enable-conditional-access"></a>Ativar o acesso condicional
Instruções detalhadas para ativar este cenário estão disponíveis nos [configuração do acesso condicional no local com o registo de dispositivos do Azure Active Directory](../../active-directory/active-directory-device-registration-on-premises-setup.md).

## <a name="troubleshooting"></a>Resolução de problemas
### <a name="the-writeback-checkbox-is-still-disabled"></a>A caixa de verificação de repetição de escrita ainda está desativada
Se a caixa de verificação para repetição de escrita do dispositivo não estiver ativada, apesar de que seguiu os passos acima, as etapas a seguir descreve o que o Assistente de instalação está a verificar antes da caixa está ativada.

Comecemos primeiro:

* A floresta em que os dispositivos estão presentes tem de ter o esquema de floresta atualizado para o nível do Windows 2012 R2 para que o objeto de dispositivo e atributos associados estão presentes.
* Se o Assistente de instalação já está em execução, em seguida, quaisquer alterações não serão detetadas. Neste caso, conclua o Assistente de instalação e executá-lo novamente.
* Certifique-se a conta fornecida no script de inicialização é, na verdade, o usuário correto utilizado pelo conector do Active Directory. Para verificar isto, siga estes passos:
  * A partir do menu Iniciar, abra **serviço de sincronização**.
  * Abra o **conectores** separador.
  * Localize o conector com o tipo de serviços de domínio do Active Directory e selecioná-lo.
  * Sob **ações**, selecione **propriedades**.
  * Aceda a **ligar à floresta do Active Directory**. Certifique-se de que o nome de utilizador e domínio especificado na correspondência ecrã, a conta fornecida para o script.
    ![Conta do conector no Gestor de serviço de sincronização](./media/how-to-connect-device-writeback/connectoraccount.png)

Verificar a configuração no Active Directory:

* Certifique-se de que o serviço de registo do dispositivo estão localizado na localização abaixo (CN = DeviceRegistrationService, CN = Services de registo do dispositivo, CN = Configuration de registo do dispositivo, CN = Services, CN = Configuration) no contexto de nomenclatura de configuração.

![Resolução de problemas, DeviceRegistrationService no espaço de nomes de configuração](./media/how-to-connect-device-writeback/troubleshoot1.png)

* Certifique-se de que existe apenas um objeto de configuração ao pesquisar o espaço de nomes de configuração. Se existir mais do que um, elimine duplicados.

![Resolução de problemas, procure os objetos duplicados](./media/how-to-connect-device-writeback/troubleshoot2.png)

* No objeto do serviço de registo do dispositivo, certificar-se de que o atributo msDS-DeviceLocation está presente e tem um valor. Pesquisa esta localização e verifique se estiver presente, com o msDS-DeviceContainer objectType.

![Resolução de problemas, msDS-DeviceLocation](./media/how-to-connect-device-writeback/troubleshoot3.png)

![Resolução de problemas, de classe de objeto de RegisteredDevices](./media/how-to-connect-device-writeback/troubleshoot4.png)

* Certifique-se de que a conta utilizada pelo conector do Active Directory tem as permissões necessárias no contentor registado dispositivos encontrado pelo passo anterior. Isso é as permissões esperadas nesse contêiner:

![Resolver problemas, verifique as permissões no contentor](./media/how-to-connect-device-writeback/troubleshoot5.png)

* Certifique-se de que a conta do Active Directory tem permissões no contêiner CN = Configuration de registo do dispositivo, CN = Services, CN = o objeto de configuração.

![Resolver problemas, verifique as permissões no Configuration de registo do dispositivo](./media/how-to-connect-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>Informação Adicional
* [Gestão de riscos com acesso condicional](../active-directory-conditional-access-azure-portal.md)
* [Configuração de acesso condicional no local com o registo de dispositivos do Azure Active Directory](../../active-directory/active-directory-device-registration-on-premises-setup.md)

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).

