---
title: Recuperar de falha na região
description: Saiba como o Azure App Service o ajuda a manter as capacidades de continuidade do negócio e recuperação de desastres (BCDR). Recupere a sua aplicação de uma falha em toda a região em Azure.
ms.topic: how-to
ms.date: 06/09/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 1388dc11254324f74efcbaa55c97cac2ccd0c026
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87073748"
---
# <a name="move-an-app-service-app-to-another-region"></a>Mover uma app de Serviço de Aplicações para outra região

Este artigo descreve como repor os recursos do Serviço de Aplicações numa região diferente do Azure durante um desastre que afeta toda uma região do Azure. Quando um desastre traz uma região inteira de Azure offline, todas as aplicações do Serviço de Aplicações hospedadas naquela região são colocadas em modo de recuperação de desastres. As funcionalidades estão disponíveis para ajudá-lo a restaurar a app para uma região diferente ou recuperar ficheiros da aplicação impactada.

Os recursos do Serviço de Aplicações são específicos da região e não podem ser movidos através de regiões. Você deve restaurar a app para uma nova app em uma região diferente, e depois criar configurações ou recursos espelhadores para a nova app.

## <a name="prerequisites"></a>Pré-requisitos

- Nenhum. [Restaurar a partir de snapshot](app-service-web-restore-snapshots.md) geralmente requer nível **Premium,** mas em modo de recuperação de desastres, é automaticamente ativado para a sua app impactada, independentemente do nível em que a app impactada está.

## <a name="prepare"></a>Preparação

Identifique todos os recursos do Serviço de Aplicações que a app impactada utiliza atualmente. Por exemplo:

- Aplicações do Serviço de Aplicações
- [Planos do Serviço de Aplicações](overview-hosting-plans.md)
- [Blocos de implementação](deploy-staging-slots.md)
- [Domínios personalizados comprados em Azure](manage-custom-dns-buy-domain.md)
- [Certificados SSL](configure-ssl-certificate.md)
- [Integração da Rede Virtual Azure](web-sites-integrate-with-vnet.md)
- [Ligações híbridas.](app-service-hybrid-connections.md)
- [Identidades geridas](overview-managed-identity.md)
- [Definições de backup](manage-backup.md)

Certos recursos, tais como certificados importados ou ligações híbridas, contêm integração com outros serviços da Azure. Para obter informações sobre como mover esses recursos através das regiões, consulte a documentação dos respetivos serviços.

## <a name="restore-app-to-a-different-region"></a>Restaurar app para uma região diferente

1. Crie uma nova aplicação de Serviço de Aplicações numa região Azure *diferente* da app impactada. Esta é a aplicação-alvo no cenário de recuperação de desastres.

1. No [portal Azure,](https://portal.azure.com)navegue para a página de gestão da app impactada. Numa região de Azure fracassada, a aplicação impactada mostra um texto de aviso. Clique no texto de aviso.

    ![Screenshot da página da aplicação impactada. É visível uma notificação de aviso que descreve a situação e fornece um link para restaurar a aplicação.](media/manage-disaster-recovery/restore-start.png)

1. Na página **'Restaurar cópia' de segurança,** configuure a operação de restauro de acordo com a tabela seguinte. Quando terminar, clique **em OK**.

   | Definição | Valor | Descrição |
   |-|-|-|
   | **Instantâneo (Pré-visualização)** | Selecione uma foto instantânea. | As duas fotos mais recentes estão disponíveis. |
   | **Destino de restauração** | **Aplicação existente** | Clique na nota abaixo que diz **Clique aqui para alterar a aplicação de destino de restauro** e selecione a aplicação-alvo. Num cenário de desastre, só é possível restaurar o instantâneo para uma aplicação numa região de Azure diferente. |
   | **Restaurar a configuração do site** | **Sim** | |

    ![Screenshot da página De backup do Restauro. Um instantâneo específico, as opções que a tabela anterior lista, e o botão OK são realçados.](media/manage-disaster-recovery/restore-configure.png)

3. Configure tudo o [resto](#prepare) na aplicação-alvo para espelhar a aplicação impactada e verificar a sua configuração.

4. Quando estiver pronto para o domínio personalizado apontar para a aplicação-alvo, [remapia o nome de domínio](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name).

## <a name="recover-app-content-only"></a>Recuperar apenas conteúdo de aplicativos

Se quiser apenas recuperar os ficheiros da aplicação impactada sem a restaurar, utilize os seguintes passos:

1. No [portal Azure,](https://portal.azure.com)navegue para a página de gestão da aplicação impactada e clique em **Obter o perfil de publicação**.

    ![Screenshot da página da aplicação impactada. Uma notificação de aviso é visível, mas não é realçada. Em vez disso, destaca-se o item de perfil de publicação Get.](media/manage-disaster-recovery/get-publish-profile.png)

1. Abra o ficheiro descarregado e encontre o perfil de publicação que contém `ReadOnly - FTP` no seu nome. Este é o perfil de recuperação de desastres. Por exemplo:

    ```xml
    <publishProfile profileName="%app-name% - ReadOnly - FTP" publishMethod="FTP" publishUrl="ftp://%ftp-site%/site/wwwroot" ftpPassiveMode="True" userName="%app-name%\$%app-name%" userPWD="" destinationAppUrl="http://%app-name%.azurewebsites.net" SQLServerDBConnectionString="" mySQLDBConnectionString="" hostingProviderForumLink="" controlPanelLink="http://windows.azure.com" webSystem="WebSites">
        <databases />
    </publishProfile>
    ```
    
    Copiar três valores de atributo: 
        
    - `publishUrl`: o nome anfitrião FTP
    - `userName` e `userPWD` : as credenciais FTP

1. Utilize o cliente FTP à sua escolha, conecte-se ao anfitrião FTP da aplicação impactada usando o nome de anfitrião e credenciais.

1. Uma vez ligado, descarregue toda a pasta */site/wwwroot.* A imagem que se segue mostra como descarrega no [FileZilla](https://filezilla-project.org/).

    ![Screenshot de uma hierarquia de ficheiros FileZilla. A pasta wwwroot é destacada e o seu menu de atalho é visível. Nesse menu, o Download está em destaque.](media/manage-disaster-recovery/download-content.png)

## <a name="next-steps"></a>Passos seguintes
[Restaurar uma aplicação em Azure a partir de um instantâneo](app-service-web-restore-snapshots.md)
