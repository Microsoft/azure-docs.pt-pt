---
title: 'Azure AD Connect: Ativar a gravação do dispositivo / Microsoft Docs'
description: Este documento detalha como ativar a gravação do dispositivo utilizando o Azure AD Connect
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
ms.topic: how-to
ms.date: 05/08/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d526394ac89e2d29b2002004736e8480bb15b954
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95973427"
---
# <a name="azure-ad-connect-enabling-device-writeback"></a>Azure AD Connect: Ativar a gravação do dispositivo
> [!NOTE]
> É necessária uma subscrição do Azure AD Premium para a gravação do dispositivo.
> 
> 

A documentação que se segue fornece informações sobre como ativar a funcionalidade de desativação do dispositivo no Azure AD Connect. O Writeback do dispositivo é utilizado nos seguintes cenários:

* Ativar [o Windows Hello for Business utilizando a implementação híbrida do certificado trust](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust-prereqs#device-registration)
* Ativar o Acesso Condicional com base em dispositivos para aplicações protegidas ADFS (2012 R2 ou superior) (confianças de partes).

Isto fornece segurança adicional e garantia de que o acesso a aplicações é concedido apenas a dispositivos fidedignos. Para obter mais informações sobre o Acesso Condicional, consulte [o Risco de Gestão com Acesso Condicional](../conditional-access/overview.md) e [Configuração de Acesso Condicional utilizando o Registo do Dispositivo de Diretório Ativo Azure](../devices/overview.md).

> [!IMPORTANT]
> <li>Os dispositivos devem estar situados na mesma floresta que os utilizadores. Uma vez que os dispositivos devem ser reensiem para uma única floresta, esta funcionalidade não suporta atualmente uma implantação com várias florestas de utilizadores.</li>
> <li>Apenas um objeto de configuração de registo do dispositivo pode ser adicionado à floresta ative directy no local. Esta funcionalidade não é compatível com uma topologia onde o Ative Directory no local é sincronizado a vários diretórios AD Azure.</li>

## <a name="part-1-install-azure-ad-connect"></a>Parte 1: Instalar Azure AD Connect
Instale o Azure AD Connect utilizando configurações personalizadas ou expressas. A Microsoft recomenda começar com todos os utilizadores e grupos sincronizados com sucesso antes de ativar a gravação do dispositivo.

## <a name="part-2-enable-device-writeback-in-azure-ad-connect"></a>Parte 2: Ativar a gravação do dispositivo no Azure AD Connect
1. Volte a executar o assistente de instalação. Selecione opções de **configuração** a partir da página Tarefas Adicionais e clique em **Seguinte**. 

    ![Opções de configuração do dispositivo](./media/how-to-connect-device-writeback/deviceoptions.png)

    >[!NOTE]
    > As novas opções do dispositivo Configurar apenas estão disponíveis na versão 1.1.819.0 e mais recentes.

2. Na página de opções do dispositivo, selecione **configurar a gravação do dispositivo**. A opção de **desativar o dispositivo** não estará disponível até que a gravação do dispositivo esteja ativada. Clique em **Seguinte** para passar para a página seguinte no assistente.
    ![Escolha o funcionamento do dispositivo](./media/how-to-connect-device-writeback/configuredevicewriteback1.png)

3. Na página de writeback, verá o domínio fornecido como a floresta de writeback do dispositivo predefinido.
   ![Instalar personalizado dispositivo a escrever a floresta alvo](./media/how-to-connect-device-writeback/writebackforest.png)

4. **A** página do contentor do dispositivo oferece a opção de preparar o diretório ativo utilizando uma das duas opções disponíveis:

    a. **Fornecer credenciais de administrador da empresa**: Se as credenciais de administrador da empresa forem fornecidas para a floresta onde os dispositivos precisam de ser reencamidas, o Azure AD Connect preparará a floresta automaticamente durante a configuração da gravação do dispositivo.

    b. **Descarregue o script PowerShell**: Azure AD Connect gera automaticamente um script PowerShell que pode preparar o diretório ativo para a gravação do dispositivo. Caso as credenciais de administrador da empresa não possam ser fornecidas no Azure AD Connect, sugere-se que descarregue o script PowerShell. Forneça o script powerShell descarregado **CreateDeviceContainer.ps1** ao administrador da empresa da floresta onde os dispositivos serão descritos de volta.
    ![Preparar floresta de diretório ativo](./media/how-to-connect-device-writeback/devicecontainercreds.png)
    
    São realizadas as seguintes operações para a preparação da floresta de diretório ativo:
    * Se já não existirem, cria e configura novos contentores e objetos sob configuração de registo de dispositivos CN=,CN=Serviços,CN=Configuração,[forest-dn].
    * Se já não existirem, cria e configura novos contentores e objetos sob CN=RegisteredDevices,[domínio-dn]. Os objetos do dispositivo serão criados neste recipiente.
    * Define as permissões necessárias na conta Azure AD Connector, para gerir dispositivos no seu Diretório Ativo.
    * Só precisa de funcionar numa floresta, mesmo que o Azure AD Connect esteja a ser instalado em várias florestas.

## <a name="verify-devices-are-synchronized-to-active-directory"></a>Verifique se os dispositivos são sincronizados ao Ative Directory
A reversão do dispositivo deve estar a funcionar corretamente. Tenha em atenção que pode levar até 3 horas para que os objetos do dispositivo sejam escritos de volta à AD.  Para verificar se os seus dispositivos estão a ser sincronizados corretamente, faça o seguinte após a conclusão das regras de sincronização:

1. Inicie o Centro de Administração do Active Directory.
2. Expandir Os Blocos registados, dentro do Domínio que está a ser federado.

   ![Dispositivos registados do Ative Directory Admin Center](./media/how-to-connect-device-writeback/devicewriteback5.png)

3. Os dispositivos registados atuais serão listados lá.

   ![Lista de dispositivos registados do Ative Directory Admin Center](./media/how-to-connect-device-writeback/devicewriteback6.png)

## <a name="enable-conditional-access"></a>Ativar o acesso condicional
Instruções detalhadas para permitir este cenário estão disponíveis na [Configuração do Acesso Condicional usando o Registo do Dispositivo de Diretório Ativo Azure](../devices/overview.md).

## <a name="troubleshooting"></a>Resolução de problemas
### <a name="the-writeback-checkbox-is-still-disabled"></a>A caixa de verificação de reprodução ainda está desativada
Se a caixa de verificação do dispositivo não estiver ativada, apesar de ter seguido os passos acima, os seguintes passos irão guiá-lo através do que o assistente de instalação está a verificar antes de a caixa estar ativada.

Primeiras coisas:

* A floresta onde os dispositivos estão presentes deve ter o esquema florestal atualizado para o nível R2 do Windows 2012 para que o objeto do dispositivo e os atributos associados estejam presentes.
* Se o assistente de instalação já estiver em funcionamento, não serão detetadas quaisquer alterações. Neste caso, complete o assistente de instalação e volte a executá-lo.
* Certifique-se de que a conta que fornece no script de inicialização é, na verdade, o utilizador correto utilizado pelo Ative Directory Connector. Para verificar isto, siga estes passos:
  * A partir do menu inicial, abra **o Serviço de Sincronização**.
  * Abra o **separador Conectores.**
  * Encontre o Conector com serviços de domínio de diretório ativo tipo e selecione-o.
  * Em **Ações**, selecione **Properties**.
  * Ir para **Ligar à Floresta de Diretório Ativo.** Verifique se o domínio e o nome de utilizador especificados neste ecrã correspondem à conta fornecida no script.
    ![Conta de conector no Gestor de Serviços de Sincronização](./media/how-to-connect-device-writeback/connectoraccount.png)

Verifique a configuração no Ative Directory:

* Verifique se o Serviço de Registo de Dispositivos está localizado no local abaixo (CN=DeviceRegistrationService,CN=Device Registration Services,CN=Device Registration Configuration,CN=Services,CN=Configuration) em contexto de nomeação de configuração.

![Resolução de problemas, Serviço de Reistração de Dispositivos no espaço de nome de configuração](./media/how-to-connect-device-writeback/troubleshoot1.png)

* Verifique se existe apenas um objeto de configuração pesquisando o espaço de nome de configuração. Se houver mais de um, apague a duplicação.

![Resolução de problemas, procura pelos objetos duplicados](./media/how-to-connect-device-writeback/troubleshoot2.png)

* No objeto de Serviço de Registo do Dispositivo, certifique-se de que o atributo MSDS-DeviceLocation está presente e tem um valor. Procure este local e certifique-se de que está presente com o objectType msDS-DeviceContainer.

![Resolução de problemas, msDS-DeviceLocation](./media/how-to-connect-device-writeback/troubleshoot3.png)

![Resolução de problemas, Classe de objetos registadosDevices](./media/how-to-connect-device-writeback/troubleshoot4.png)

* Verifique se a conta utilizada pelo Ative Directory Connector exigiu permissões no contentor de Dispositivos Registados encontrado pelo passo anterior. Estas são as permissões esperadas neste contentor:

![Resolução de problemas, verificar permissões no contentor](./media/how-to-connect-device-writeback/troubleshoot5.png)

* Verifique se a conta ative Directory tem permissões na configuração de registo de dispositivos CN=,CN=Serviços,CN=Configuração.

![Resolução de problemas, verificar permissões na configuração de registo do dispositivo](./media/how-to-connect-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>Informações adicionais
* [Gestão de risco com acesso condicional](../conditional-access/overview.md)
* [Criação de Acesso Condicional no local utilizando o registo de dispositivos de diretório ativo Azure](../devices/overview.md)

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).