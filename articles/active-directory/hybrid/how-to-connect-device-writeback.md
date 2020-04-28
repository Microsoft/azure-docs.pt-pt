---
title: 'Azure AD Connect: Habilitar o dispositivo reemreserva / Microsoft Docs'
description: Este documento detalha como ativar a reescrita do dispositivo usando o Azure AD Connect
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
ms.openlocfilehash: 632f6f80184c6ba3409bd30ae070cbaefc77f036
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67109505"
---
# <a name="azure-ad-connect-enabling-device-writeback"></a>Azure AD Connect: Habilitar a reescrita do dispositivo
> [!NOTE]
> É necessária uma subscrição do Azure AD Premium para a reescrita do dispositivo.
> 
> 

A seguinte documentação fornece informações sobre como ativar a funcionalidade de reescrita do dispositivo no Azure AD Connect. A Writeback do dispositivo é utilizada nos seguintes cenários:

* Ativar [o Windows Hello for Business utilizando a implementação de confiança](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust-prereqs#device-registration) de certificado híbrido
* Ativar o Acesso Condicional com base em dispositivos para aplicações protegidas ADFS (2012 R2 ou superior) (trusts trusts de partido).

Isto fornece segurança adicional e garantia de que o acesso a aplicações é concedido apenas a dispositivos fidedignos. Para obter mais informações sobre acesso condicional, consulte [gerir o risco com acesso condicional](../active-directory-conditional-access-azure-portal.md) e configurar o acesso condicional no local utilizando o Registo de Dispositivos de [Diretório Ativo Azure](../../active-directory/active-directory-device-registration-on-premises-setup.md).

> [!IMPORTANT]
> <li>Os dispositivos devem estar localizados na mesma floresta que os utilizadores. Uma vez que os dispositivos devem ser reescritos para uma única floresta, esta funcionalidade não suporta atualmente uma implantação com múltiplas florestas de utilizadores.</li>
> <li>Apenas um objeto de configuração de registo de dispositivos pode ser adicionado à floresta de Diretório Ativo no local. Esta funcionalidade não é compatível com uma topologia onde o Diretório Ativo no local é sincronizado com vários diretórios da AD Azure.</li>

## <a name="part-1-install-azure-ad-connect"></a>Parte 1: Instalar ligação ad azure
Instale o Azure AD Connect utilizando as definições personalizadas ou express. A Microsoft recomenda começar com todos os utilizadores e grupos sincronizados com sucesso antes de ativar a reescrita do dispositivo.

## <a name="part-2-enable-device-writeback-in-azure-ad-connect"></a>Parte 2: Ativar a reescrita do dispositivo no Azure AD Connect
1. Volte a executar o assistente de instalação. **Selecione configurar as opções** do dispositivo a partir da página De Tarefas Adicionais e clique em **Next**. 

    ![Configure as opções do dispositivo](./media/how-to-connect-device-writeback/deviceoptions.png)

    >[!NOTE]
    > As novas opções do dispositivo Configure só estão disponíveis na versão 1.1.819.0 e mais recentes.

2. Na página de opções do dispositivo, **selecione A devolução**do dispositivo Configure . A opção de **desativar** a reprodução do dispositivo não estará disponível até que a reescrita do dispositivo esteja ativada. Clique em **Next** para passar para a página seguinte no assistente.
    ![Escolha operação do dispositivo](./media/how-to-connect-device-writeback/configuredevicewriteback1.png)

3. Na página de redação, verá o domínio fornecido como a floresta de reescrita padrão do Dispositivo.
   ![Instalação personalizada dispositivo writeback target forest](./media/how-to-connect-device-writeback/writebackforest.png)

4. **A** página do contentor do dispositivo oferece a opção de preparar o diretório ativo utilizando uma das duas opções disponíveis:

    a. **Fornecer credenciais**de administrador da empresa : Se forem fornecidas credenciais de administrador da empresa para a floresta onde os dispositivos precisam de ser reescritos, o Azure AD Connect preparará a floresta automaticamente durante a configuração da reescrita do dispositivo.

    b. **Baixar o script PowerShell**: Azure AD Connect gera um script PowerShell que pode preparar o diretório ativo para a reescrita do dispositivo. Caso as credenciais do administrador da empresa não possam ser fornecidas no Azure AD Connect, é sugerido que descarregue o script PowerShell. Forneça o script PowerShell descarregado **CreateDeviceContainer.psq** ao administrador da empresa da floresta onde os dispositivos serão reescritos.
    ![Preparar floresta de diretório ativo](./media/how-to-connect-device-writeback/devicecontainercreds.png)
    
    São realizadas as seguintes operações para a preparação da floresta de diretórios ativo:
    * Se já não existirem, cria e configura novos contentores e objetos sob configuração de registo de dispositivos CN=Dispositivo,CN=Serviços,CN=Configuração,[forest-dn].
    * Se já não existirem, cria e configura novos recipientes e objetos ao abrigo de CN=RegisteredDevices,[domain-dn]. Serão criados objetos de dispositivo neste recipiente.
    * Define as permissões necessárias na conta do Conector AD Azure, para gerir os dispositivos no seu Diretório Ativo.
    * Só precisa de funcionar numa floresta, mesmo que o Azure AD Connect esteja a ser instalado em várias florestas.

## <a name="verify-devices-are-synchronized-to-active-directory"></a>Verificar que os dispositivos estão sincronizados com o Diretório Ativo
A redução do dispositivo deve agora estar a funcionar corretamente. Esteja ciente de que pode levar até 3 horas para que os objetos do dispositivo sejam reescritos em AD.  Para verificar se os seus dispositivos estão a ser sincronizados corretamente, faça o seguinte após as regras de sincronização estarem completas:

1. Inicie o Centro de Administração do Active Directory.
2. Expandir Dispositivos Registados, dentro do Domínio que está a ser federado.

   ![Dispositivos registados do Centro de Administração de Directórioativo Ativo](./media/how-to-connect-device-writeback/devicewriteback5.png)

3. Os atuais dispositivos registados serão listados lá.

   ![Lista de dispositivos registados do Centro de Administração ativa](./media/how-to-connect-device-writeback/devicewriteback6.png)

## <a name="enable-conditional-access"></a>Ativar o acesso condicional
Instruções detalhadas para ativar este cenário estão disponíveis na Configuração de Acesso Condicional no local utilizando o Registo de Dispositivos de [Diretório Ativo Azure](../../active-directory/active-directory-device-registration-on-premises-setup.md).

## <a name="troubleshooting"></a>Resolução de problemas
### <a name="the-writeback-checkbox-is-still-disabled"></a>A caixa de verificação de redação ainda está desativada
Se a caixa de verificação para a reescrita do dispositivo não estiver ativada, apesar de ter seguido os passos acima, os seguintes passos guiar-lhe-ão através do que o assistente de instalação está a verificar antes de a caixa estar ativada.

Primeiras coisas primeiro:

* A floresta onde os dispositivos estão presentes deve ter o esquema florestal atualizado para o nível R2 do Windows 2012 para que o objeto do dispositivo e os atributos associados estejam presentes .
* Se o assistente de instalação já estiver em funcionamento, então não serão detetadas alterações. Neste caso, complete o assistente de instalação e volte a executá-lo.
* Certifique-se de que a conta que fornece no script de inicialização é na verdade o utilizador correto utilizado pelo Conector de Diretório Ativo. Para verificar isto, siga estes passos:
  * Desde o menu inicial, abra o **Serviço de Sincronização.**
  * Abra o separador **Conectores.**
  * Encontre o Conector com serviços de domínio de diretório ativo do tipo e selecione-o.
  * Em **Ações,** selecione **Propriedades**.
  * Ir para **Connect to Ative Directory Forest**. Verifique se o domínio e o nome de utilizador especificados neste ecrã correspondem à conta fornecida ao script.
    ![Conta conector no Gestor de Serviçosincronizado](./media/how-to-connect-device-writeback/connectoraccount.png)

Verifique a configuração no Diretório Ativo:

* Verifique se o Serviço de Registo do Dispositivo está localizado no local abaixo (CN=DeviceRegistrationService,CN=Device Registration Services,CN=Device Registration Configuration,CN=Services,CN=Configuração) no contexto de nomeação de configuração.

![Troubleshoot, DeviceRegistrationRegistrationService no espaço de nome de configuração](./media/how-to-connect-device-writeback/troubleshoot1.png)

* Verifique se só existe um objeto de configuração, pesquisando no espaço de nome de configuração. Se houver mais de um, elimine o duplicado.

![Resolução de problemas, procura os objetos duplicados](./media/how-to-connect-device-writeback/troubleshoot2.png)

* No objeto do Serviço de Registo de Dispositivos, certifique-se de que o atributo msDS-DeviceLocation está presente e tem um valor. Procure este local e certifique-se de que está presente com o objecttype msDS-DeviceContainer.

![Resolução de problemas, msDS-DeviceLocation](./media/how-to-connect-device-writeback/troubleshoot3.png)

![Resolução de problemas, classe de objetos RegisteredDevices](./media/how-to-connect-device-writeback/troubleshoot4.png)

* Verifique se a conta utilizada pelo Conector de Diretório Ativo exigiu permissões no recipiente de Dispositivos Registados encontrados pelo passo anterior. Estas são as permissões esperadas neste recipiente:

![Resolução de problemas, verificar permissões no recipiente](./media/how-to-connect-device-writeback/troubleshoot5.png)

* Verifique se a conta De Diretório Ativo tem permissões na Configuração de Registo de Dispositivos CN=Dispositivo,CN=Services,CN=Configuração do objeto.

![Resolução de problemas, verificar permissões na configuração de registo do dispositivo](./media/how-to-connect-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>Informações adicionais
* [Gerir o risco com acesso condicional](../active-directory-conditional-access-azure-portal.md)
* [Criação de acesso condicional no local utilizando o Registo de Dispositivos de Diretório Ativo Azure](../../active-directory/active-directory-device-registration-on-premises-setup.md)

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).

