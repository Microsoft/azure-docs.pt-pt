---
title: StorSimple 8000 série segurança Microsoft Docs
description: Descreve as funcionalidades de segurança e privacidade que protegem o seu serviço, dispositivo e dados StorSimple nas instalações e na nuvem.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/18/2018
ms.author: alkohli
ms.openlocfilehash: 72bf252488878c8b6fabb2ea2d61658a2cd87e14
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966212"
---
# <a name="storsimple-security-and-data-protection"></a>StorSimple segurança e proteção de dados

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Descrição geral

A segurança é uma grande preocupação para quem está a adotar uma nova tecnologia, especialmente quando a tecnologia é utilizada com dados confidenciais ou proprietários. Ao avaliar diferentes tecnologias, deve considerar riscos e custos acrescidos para a proteção de dados. O Microsoft Azure StorSimple fornece uma solução de segurança e privacidade para proteção de dados, ajudando a garantir:

* **Confidencialidade** – Só as entidades autorizadas podem visualizar os seus dados.
* **Integridade** – Só as entidades autorizadas podem modificar ou eliminar os seus dados.

A solução Microsoft Azure StorSimple é composta por quatro componentes principais que interagem entre si:

* **O serviço StorSimple Device Manager alojado no Microsoft Azure** – o serviço de gestão que utiliza para configurar e providenciar o dispositivo StorSimple.
* **Dispositivo StorSimple** – Um dispositivo físico instalado no seu datacenter. Todos os anfitriões e clientes que geram dados conectam-se ao dispositivo StorSimple, e o dispositivo gere os dados e move-os para a nuvem Azure conforme apropriado.
* **Clientes/anfitriões ligados ao dispositivo** – Os clientes da sua infraestrutura que se ligam ao dispositivo StorSimple e geram dados que precisam de ser protegidos.
* **Armazenamento na cloud** – A localização na cloud do Azure onde os dados são armazenados.

As seguintes secções descrevem as funcionalidades de segurança StorSimple que ajudam a proteger cada um destes componentes e os dados armazenados nos mesmos. Também inclui uma lista de perguntas que poderá ter sobre a segurança microsoft Azure StorSimple e as respostas correspondentes.

## <a name="storsimple-device-manager-service-protection"></a>Proteção de serviço StorSimple Device Manager

O serviço StorSimple Device Manager é um serviço de gestão hospedado no Microsoft Azure e utilizado para gerir todos os dispositivos StorSimple que a sua organização adquiriu. Pode aceder ao serviço StorSimple Device Manager utilizando as suas credenciais organizacionais para iniciar sessão no portal Azure através de um navegador web.

O acesso ao serviço StorSimple Device Manager requer que a sua organização tenha uma subscrição Azure que inclui storSimple. A subscrição controla as funcionalidades a que pode aceder no portal do Azure. Se a sua organização não tiver uma subscrição do Azure e quiser saber mais sobre eles, consulte [o Azure como uma organização.](../active-directory/fundamentals/sign-up-organization.md)

Uma vez que o serviço StorSimple Device Manager está hospedado no Azure, está protegido pelas funcionalidades de segurança Azure. Para obter mais informações sobre as funcionalidades de segurança fornecidas pelo Microsoft Azure, aceda ao [Centro de Fidedignidade do Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/).

## <a name="storsimple-device-protection"></a>Proteção do dispositivo StorSimple

O dispositivo StorSimple é um dispositivo de armazenamento híbrido no local que contém unidades de estado sólido (SSDs) e discos rígidos (HDDs), juntamente com controladores redundantes e capacidades automáticas de failover. Os controladores gerem o tiering de armazenamento, colocando dados atualmente utilizados (ou quentes) no armazenamento local (no dispositivo StorSimple ou nos servidores no local), deslocando os dados menos utilizados para a nuvem.

Apenas dispositivos autorizados StorSimple podem aderir ao serviço StorSimple Device Manager que criou na sua subscrição Azure. Para autorizar um dispositivo, deve registá-lo com o serviço StorSimple Device Manager, fornecendo a chave de registo de serviço. A chave de registo de serviço é uma chave aleatória de 128 bits gerada no portal Azure.

![Chave de registo de serviço](./media/storsimple-security/ServiceRegistrationKey.png)

Para saber como obter uma chave de registo de serviço, vá ao [Passo 2: Obtenha a chave de registo de serviço.](storsimple-8000-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key)

A chave de registo de serviço é uma chave longa que contém mais de 100 caracteres. Pode copiar a chave e guardá-la num ficheiro de texto num local seguro para que possa usá-la para autorizar dispositivos adicionais, se necessário. Se a chave de registo de serviço for perdida depois de registar o seu primeiro dispositivo, pode gerar uma nova chave a partir do serviço StorSimple Device Manager. Isto não afetará o funcionamento dos dispositivos existentes.

Depois de um dispositivo ser registado, utiliza fichas para comunicar com o Microsoft Azure. A chave de registo de serviço não é utilizada após o registo do dispositivo.

> [!NOTE]
> Recomendamos que regenerar a chave de registo de serviço após cada utilização.


## <a name="protect-your-storsimple-solution-via-passwords"></a>Proteja a sua solução StorSimple através de senhas

As palavras-passe são um aspeto importante da segurança do computador e são utilizadas extensivamente na solução StorSimple para ajudar a garantir que os seus dados são acessíveis apenas aos utilizadores autorizados. A StorSimple permite-lhe configurar as seguintes palavras-passe:

* Senha de administrador de dispositivo StorSimple
* Desafiar o protocolo de autenticação do aperto de mão (CHAP) e as palavras-passe-alvo
* Palavra-passe do Snapshot Manager StorSimple

### <a name="windows-powershell-for-storsimple-and-the-storsimple-device-administrator-password"></a>Windows PowerShell para StorSimple e a palavra-passe do administrador do dispositivo StorSimple

O Windows PowerShell para StorSimple é uma interface de linha de comando que pode utilizar para gerir o dispositivo StorSimple. O Windows PowerShell para StorSimple possui funcionalidades que lhe permitem registar o seu dispositivo, configurar a interface de rede no seu dispositivo, instalar certos tipos de atualizações, resolver problemas com o seu dispositivo, acedendo à sessão de suporte e alterar o estado do dispositivo. Pode aceder ao Windows PowerShell para StorSimple ligando-o à consola em série no dispositivo ou utilizando o remoting Do Windows PowerShell.

A remoing powerShell pode ser feita através de HTTPS ou HTTP. Se a gestão remota sobre HTTPS estiver ativada, terá de descarregar o certificado de gestão remota do dispositivo e instalá-lo no cliente remoto. Para obter mais informações sobre a remoing PowerShell, vá [para Ligar remotamente ao seu dispositivo StorSimple](storsimple-8000-remote-connect.md).

Depois de utilizar o Windows PowerShell para que o StorSimple se conecte ao dispositivo, terá de fornecer a palavra-passe do administrador do dispositivo para iniciar sessão no dispositivo.

![Palavra-passe do administrador do dispositivo](./media/storsimple-security/DeviceAdminPW.png)

Tenha em mente as seguintes boas práticas:

* A gestão remota é desligada por defeito. Pode utilizar o serviço StorSimple Device Manager para o ativar. Como boas práticas de segurança, o acesso remoto só deve ser ativado durante o período de tempo em que é efetivamente necessário.
* Se alterar a palavra-passe, certifique-se de notificar todos os utilizadores de acesso remoto para que não experimentem uma perda inesperada de conectividade.
* O serviço StorSimple Device Manager não consegue recuperar as palavras-passe existentes: só as pode repor. Recomendamos que guarde todas as palavras-passe num local seguro para que não tenha de redefinir uma palavra-passe se esta for esquecida. Se precisar de redefinir uma palavra-passe, certifique-se de notificar todos os utilizadores antes de a redefinir.

Pode aceder à interface Do Windows PowerShell utilizando uma ligação em série ao dispositivo. Também pode aceder remotamente utilizando HTTP ou HTTPS, que fornecem segurança adicional. HTTPS fornece um nível de segurança mais elevado do que uma ligação em série ou HTTP. No entanto, para utilizar o HTTPS, tem primeiro de instalar um certificado no computador cliente que aceda ao dispositivo. Pode descarregar o certificado de acesso remoto a partir da página de configuração do dispositivo no serviço StorSimple Device Manager. Se o certificado de acesso remoto for perdido, deve descarregar um novo certificado e propondo-o a todos os clientes autorizados a utilizar a gestão remota.

### <a name="challenge-handshake-authentication-protocol-chap-initiator-and-target-passwords"></a>Desafiar o protocolo de autenticação do aperto de mão (CHAP) e as palavras-passe-alvo

Chap é um esquema de autenticação utilizado pelo dispositivo StorSimple para validar a identidade de clientes remotos. A verificação baseia-se numa senha partilhada. Chap pode ser unidirecional ou mútuo (bidirecional). Com chap unidirecional, o alvo (o dispositivo StorSimple) autentica um iniciador (hospedeiro). Chap mútuo ou reverso requer que o alvo autuada o iniciador e, em seguida, o iniciador autentica o alvo. O seu StorSimple pode ser configurado para utilizar qualquer um dos métodos.

Esteja atento ao seguinte momento em que configurar CHAP:

* O nome de utilizador CHAP deve conter menos de 233 caracteres.
* A palavra-passe CHAP deve ter entre 12 e 16 caracteres. A tentativa de utilização de um nome de utilizador ou palavra-passe mais longo resultará numa falha de autenticação no anfitrião do Windows.
* Não é possível utilizar a mesma palavra-passe tanto para o iniciador CHAP como para o alvo CHAP.
* Depois de definir a palavra-passe, pode ser alterada, mas não pode ser recuperada. Se a palavra-passe for alterada, certifique-se de que notifica todos os utilizadores de acesso remoto para que possam ligar-se com sucesso ao dispositivo StorSimple.

Para obter mais informações sobre chap e como configurá-lo para a sua solução StorSimple, vá ao [Configure CHAP para o seu dispositivo StorSimple](storsimple-8000-configure-chap.md).

### <a name="storsimple-snapshot-manager-password"></a>Palavra-passe do Snapshot Manager StorSimple

StorSimple Snapshot Manager é um snap-in da Microsoft Management Console (MMC) que utiliza grupos de volume e o Serviço de Cópia Sombra do Volume do Windows para gerar cópias de segurança consistentes com aplicações. Além disso, pode utilizar o StorSimple Snapshot Manager para criar horários de backup e clonar ou restaurar volumes.

Quando configurar um dispositivo para utilizar o StorSimple Snapshot Manager, será obrigado a fornecer a palavra-passe do Gestor instantâneo StorSimple. Esta palavra-passe é definida pela primeira vez no Windows PowerShell para StorSimple durante o registo. A palavra-passe também pode ser definida e alterada a partir do serviço StorSimple Device Manager. Esta palavra-passe autentica o dispositivo com o StorSimple Snapshot Manager.

![Palavra-passe do Snapshot Manager StorSimple](./media/storsimple-security/SnapshotMgrPassword.png)

A palavra-passe StorSimple Snapshot Manager deve ter entre 14 a 15 caracteres e deve conter 3 ou mais de uma combinação de caracteres maiúsculas, minúsculas, numéricas e especiais. Depois de definir a palavra-passe StorSimple Snapshot Manager, pode ser alterada, mas não pode ser recuperada. Se alterar a palavra-passe, certifique-se de notificar todos os utilizadores remotos.

Para mais informações sobre o StorSimple Snapshot Manager, aceda ao [Que é o StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="password-best-practices"></a>Melhores práticas de palavra-passe

Recomendamos que utilize as seguintes diretrizes para ajudar a garantir que as palavras-passe StorSimple sejam fortes e bem protegidas:

* Mude as suas palavras-passe a cada três meses. A alteração das palavras-passe é aplicada anualmente.
* Use senhas fortes. Para mais informações, vá para [criar senhas mais fortes e proteja-as.](https://cloudblogs.microsoft.com/microsoftsecure/2014/08/25/create-stronger-passwords-and-protect-them/)
* Utilize sempre senhas diferentes para diferentes mecanismos de acesso; cada uma das palavras-passe que especifique deve ser única.
* Não partilhe palavras-passe com ninguém que não esteja autorizado a aceder ao dispositivo StorSimple.
* Não fale de uma palavra-passe à frente de outras pessoas nem indique o formato de uma palavra-passe.
* Se suspeitar que uma conta ou senha foi comprometida, reporte o incidente ao seu departamento de segurança de informação.
* Trate todas as palavras-passe como informações confidenciais e confidenciais. 

## <a name="storsimple-data-protection"></a>Proteção de dados StorSimple

Esta secção descreve as funcionalidades de segurança StorSimple que protegem os dados em trânsito e os dados armazenados.

Como descrito noutras secções, as palavras-passe são usadas para autorizar e autenticar os utilizadores antes de poderem ter acesso à sua solução StorSimple. Outra consideração de segurança é proteger os dados de utilizadores não autorizados enquanto estes estão a ser transferidos entre sistemas de armazenamento e enquanto estes estão a ser armazenados. As seguintes secções descrevem as funcionalidades de proteção de dados fornecidas com StorSimple.

> [!NOTE]
> A deduplica fornece proteção adicional para os dados armazenados no dispositivo StorSimple e no armazenamento microsoft Azure. Quando os dados são desduplicados, os objetos de dados são armazenados separadamente dos metadados utilizados para mapear e aceder aos mesmos: não existe um contexto de nível de armazenamento disponível para reconstruir os dados com base na estrutura de volume, sistema de ficheiros ou nome de ficheiro.


## <a name="protect-data-flowing-through-the-service"></a>Proteger os dados que fluem através do serviço

O objetivo principal do serviço StorSimple Device Manager é gerir e configurar o dispositivo StorSimple. O serviço StorSimple Device Manager funciona no Microsoft Azure. Utiliza o portal Azure para introduzir dados de configuração do dispositivo e, em seguida, o Microsoft Azure utiliza o serviço StorSimple Device Manager para enviar os dados para o dispositivo. A StorSimple utiliza um sistema de pares chave assimétricos para ajudar a garantir que um compromisso do serviço Azure não resultará num compromisso de informações armazenadas.

![Encriptação de dados em voo](./media/storsimple-security/DataEncryption.png)

O sistema chave assimétrico ajuda a proteger os dados que fluem através do serviço da seguinte forma:

1. Um certificado de encriptação de dados que utiliza um par de chaves público e privado assimétrico é gerado no dispositivo e é usado para proteger os dados. As teclas são geradas quando o primeiro dispositivo está registado.
2. As teclas de certificado de encriptação de dados são exportadas para um ficheiro Personal Information Exchange (.pfx) que está protegido pela chave de encriptação de dados de serviço, que é uma chave forte de 128 bits que é gerada aleatoriamente pelo primeiro dispositivo durante o registo.
3. A chave pública do certificado é seguramente disponibilizada ao serviço StorSimple Device Manager, e a chave privada permanece com o dispositivo.
4. Os dados que entram no serviço são encriptados utilizando a chave pública e desencriptados utilizando a chave privada armazenada no dispositivo, garantindo que o serviço Azure não pode desencriptar os dados que fluem para o dispositivo.

A chave de encriptação de dados de serviço é gerada apenas no primeiro dispositivo registado no serviço. Todos os dispositivos subsequentes registados no serviço devem utilizar a mesma chave de encriptação de dados de serviço.

> [!IMPORTANT]
> É muito importante fazer uma cópia da chave de encriptação de dados de serviço e guardá-la num local seguro. Uma cópia da chave de encriptação de dados de serviço deve ser armazenada de modo a poder ser acedida por uma pessoa autorizada e pode ser facilmente comunicada ao administrador do dispositivo.
> 
> Se a chave de encriptação de dados de serviço for perdida, uma pessoa de suporte da Microsoft pode ajudá-lo a recuperá-la desde que tenha pelo menos um dispositivo num estado online. Recomendamos que altere a chave de encriptação de dados de serviço após a sua recuperação.

Para alterar a chave de encriptação de dados de serviço e o certificado de encriptação de dados correspondente, siga os passos na [alteração da chave de encriptação de dados de serviço para o seu serviço StorSimple Device Manager](storsimple-8000-manage-service.md#change-the-service-data-encryption-key). A alteração das teclas de encriptação requer que todos os dispositivos sejam atualizados com a nova chave. Por isso, recomendamos que altere a chave quando todos os dispositivos estiverem online. Se os dispositivos estiverem desligados, as suas chaves podem ser trocadas num momento diferente. Os dispositivos com teclas desatualizadas ainda poderão executar backups, mas não poderão restaurar os dados até que a chave seja atualizada.

A chave de encriptação de dados de serviço e o certificado de encriptação de dados não expiram. No entanto, recomendamos que altere anualmente a chave de encriptação de dados de serviço para ajudar a prevenir o compromisso chave.

## <a name="protect-data-at-rest"></a>Proteger os dados inativos

O dispositivo StorSimple gere os dados armazenando-os em camadas locais e na nuvem, dependendo da frequência de utilização. Todas as máquinas hospedeiras que estão ligadas ao dispositivo enviam dados para o dispositivo, que depois move os dados para a nuvem, conforme apropriado. Os dados são transferidos do dispositivo para a nuvem de forma segura através da Internet. Cada dispositivo tem um alvo iSCSI que aparece todos os volumes partilhados nesse dispositivo. Todos os dados são encriptados antes de serem enviados para o armazenamento na nuvem. 

![Chave de encriptação de armazenamento em nuvem](./media/storsimple-security/CloudStorageEncryption.png)

Para ajudar a garantir a segurança e integridade dos dados movidos para a nuvem, o StorSimple permite definir as chaves de encriptação de armazenamento em nuvem da seguinte forma:

* Especifica a chave de encriptação de armazenamento em nuvem quando cria um recipiente de volume. A chave não pode ser modificada ou adicionada mais tarde.
* Todos os volumes num recipiente de volume partilham a mesma chave de encriptação. Se quiser uma forma diferente de encriptação para um volume específico, recomendamos que crie um novo recipiente de volume para hospedar esse volume.
* Quando introduz a chave de encriptação de armazenamento em nuvem no serviço StorSimple Device Manager, a chave é encriptada utilizando a parte pública da chave de encriptação de dados de serviço e depois enviada para o dispositivo.
* A chave de encriptação de armazenamento em nuvem não é armazenada em nenhum lugar do serviço e é conhecida apenas pelo dispositivo.
* Especificar uma chave de encriptação de armazenamento em nuvem é opcional. Pode enviar dados encriptados no anfitrião para o dispositivo.

### <a name="additional-security-best-practices"></a>Melhores práticas adicionais de segurança

* Tráfego dividido: isolar o seu iSCSI SAN do tráfego de utilizadores numa LAN corporativa, implantando uma rede totalmente separada e utilizando VLANs onde o isolamento físico não é uma opção. Uma rede dedicada para armazenamento iSCSI garantirá a segurança e o desempenho dos seus dados críticos do negócio. A mistura de armazenamento e tráfego de utilizadores sobre uma LAN corporativa não é recomendada e pode aumentar a latência e causar falhas na rede.
* Para a segurança da rede do lado do hospedeiro, utilize interfaces de rede que suportem o motor de descarregamento TCP/IP (TOE). O TOE reduz a carga de CPU processando o TCP no adaptador de rede.

## <a name="protect-data-via-storage-accounts"></a>Proteger dados através de contas de armazenamento

Cada subscrição do Microsoft Azure pode criar uma ou mais contas de armazenamento. (Uma conta de armazenamento fornece um espaço de nome único para trabalhar com dados armazenados na nuvem Azure.) O acesso a uma conta de armazenamento é controlado pelas chaves de subscrição e acesso associadas a essa conta de armazenamento.

Quando cria uma conta de armazenamento, o Microsoft Azure gera duas teclas de acesso ao armazenamento de 512 bits, uma das quais é utilizada para autenticação quando o dispositivo StorSimple acede à conta de armazenamento. Note que apenas uma destas chaves está a ser utilizada. A outra chave é mantida em reserva, permitindo-lhe rodar as teclas periodicamente. Para rodar as teclas, torna a tecla secundária ativa e, em seguida, apaga a tecla primária. Em seguida, pode criar uma nova chave para utilização durante a próxima rotação. (Por razões de segurança, muitos centros de dados requerem rotação de chaves.)

Recomendamos que siga estas boas práticas para a rotação das chaves:

* Deve rodar regularmente as chaves da conta de armazenamento para ajudar a garantir que a sua conta de armazenamento não é acedida por utilizadores não autorizados.
* Periodicamente, o seu administrador Azure deve alterar ou regenerar a tecla primária ou secundária utilizando a secção de Armazenamento do portal Azure para aceder diretamente à conta de armazenamento.

## <a name="protect-data-via-encryption"></a>Proteger dados através da encriptação

A StorSimple utiliza os seguintes algoritmos de encriptação para proteger os dados armazenados ou viajando entre os componentes da sua solução StorSimple.

| Algoritmo | Comprimento da chave | Protocolos/aplicações/comentários |
| --- | --- | --- |
| RSA |2048 |O RSA PKCS 1 v1.5 é utilizado pelo portal Azure para encriptar dados de configuração enviados para o dispositivo: por exemplo, credenciais de conta de armazenamento, configuração do dispositivo StorSimple e chaves de encriptação de armazenamento em nuvem. |
| AES |256 |O AES com CBC é utilizado para encriptar a parte pública da chave de encriptação de dados de serviço antes de ser enviada para o portal Azure a partir do dispositivo StorSimple. Também é utilizado pelo dispositivo StorSimple para encriptar dados antes de os dados serem enviados para a conta de armazenamento na nuvem. |

## <a name="storsimple-cloud-appliance-security"></a>Segurança do aparelho em nuvem StorSimple

[!INCLUDE [storsimple Cloud Appliance security](../../includes/storsimple-virtual-device-security.md)]

## <a name="managing-personal-information"></a>Gestão de informações pessoais

O Gestor de Dispositivos StorSimple para séries físicas e virtuais recolhe informações pessoais nos seguintes casos principais:

- Alerte as definições do utilizador onde o endereço de e-mail dos utilizadores está configurado. Estas informações podem ser vistas e apuradas pelo administrador. Isto aplica-se tanto aos dispositivos da série StorSimple 8000 como às Matrizes Virtuais StorSimple.
  * Para ver e limpar as definições para a série StorSimple 8000, siga os passos em [Ver e gere os alertas StorSimple](storsimple-8000-manage-alerts.md#configure-alert-settings)
  * Para visualizar e limpar as definições para StorSimple Virtual Array, siga os passos em [Visualização e gere os alertas StorSimple](storsimple-virtual-array-manage-alerts.md#configure-alert-settings)
- Utilizadores que possam aceder aos dados que residem nas ações. Uma lista de utilizadores que podem aceder aos dados da partilha é exibida e pode ser visualizada. Esta lista também é eliminada quando as ações são eliminadas. Isto aplica-se apenas a Arrays Virtuais StorSimple.
  * Para ver a lista de utilizadores que podem aceder ou eliminar uma partilha, siga os passos em [Gerir ações no StorSimple Virtual Array](storsimple-virtual-array-manage-shares.md)

Para obter mais informações, reveja a política de privacidade da Microsoft no [Centro de Fidedignidade](https://www.microsoft.com/trustcenter).

## <a name="frequently-asked-questions-faq"></a>Perguntas Frequentes (FAQ)

Seguem-se algumas perguntas e respostas sobre segurança e Microsoft Azure StorSimple.

**Q:** O meu serviço está comprometido. Quais devem ser os meus próximos passos?

**A:** Deve alterar imediatamente a chave de encriptação de dados de serviço e as chaves da conta de armazenamento para a conta de armazenamento que está a ser utilizada para a tiering de dados. Para instruções, vá a:

* [Alterar a chave de encriptação de dados de serviço](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Rotação chave das contas de armazenamento](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**Q:** Tenho um novo dispositivo StorSimple que está a pedir a chave de registo de serviço. Como posso recuperá-la?

**A:** Esta chave foi criada quando criou o serviço StorSimple Device Manager. Quando utilizar o serviço StorSimple Device Manager para ligar ao dispositivo, pode utilizar a página de arranque rápida do serviço para visualizar ou regenerar a chave de registo de serviço. Gerar uma nova chave de registo de serviço não afetará os dispositivos registados existentes. Para instruções, vá a:

* [Ver ou regenerar a chave de registo de serviço](storsimple-8000-manage-service.md#regenerate-the-service-registration-key)

**Q:** Perdi a minha chave de encriptação de dados de serviço. O que posso fazer?

**A:** Contacte o Microsoft Support. Podem iniciar sessão de suporte no seu dispositivo e ajudá-lo a recuperar a chave (desde que pelo menos um dispositivo esteja online). Imediatamente após obter a chave de encriptação de dados de serviço, deverá alterá-la para garantir que a nova chave é conhecida apenas por si. Para instruções, vá a:

* [Alterar a chave de encriptação de dados de serviço](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)

**Q:**  Autorizei um dispositivo para uma alteração da chave de encriptação de dados de serviço, mas não iniciei o processo de alteração da chave. O que devo fazer?

**A:** Se o período de tempo tiver expirado, terá de reautorizar o dispositivo para alterar a chave de encriptação de dados de serviço e recomeçar o processo.

**Q:**  Mudei a chave de encriptação de dados de serviço, mas não fui capaz de atualizar os outros dispositivos em 4 horas. Tenho que começar de novo?

**A:** O período de 4 horas é apenas para dar início à mudança. Depois de iniciar o processo de atualização no dispositivo autorizado StorSimple, a autorização é válida até que todos os dispositivos sejam atualizados.

**Q:** O nosso administrador da StorSimple deixou a empresa. O que devo fazer?

**A:** Altere e rep reset as palavras-passe que permitam o acesso ao dispositivo StorSimple e altere a chave de encriptação de dados de serviço para garantir que as novas informações não são conhecidas por pessoal não autorizado. Para instruções, vá a:

* [Utilize o serviço StorSimple Device Manager para alterar as suas palavras-passe storsimple](storsimple-8000-change-passwords.md)
* [Alterar a chave de encriptação de dados de serviço](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Configure CHAP para o seu dispositivo StorSimple](storsimple-8000-configure-chap.md)

**Q:** Quero fornecer a palavra-passe do StorSimple Snapshot Manager a um anfitrião que está a ligar-se ao dispositivo StorSimple, mas a palavra-passe não está disponível. Que posso eu fazer?

**A:** Se esqueceu a senha, deve criar uma nova. Em seguida, certifique-se de informar todos os utilizadores existentes que a palavra-passe foi alterada e que devem atualizar os seus clientes para usar a nova senha. Para instruções, vá a:

* [Alterar a palavra-passe do Gestor instantâneo StorSimple](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password)
* [Autenticar um dispositivo](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**Q:** O certificado de acesso remoto ao Windows PowerShell para StorSimple foi alterado no dispositivo. Como actualo os meus clientes de acesso remoto?

**A:** Pode descarregar o novo certificado do serviço StorSimple Device Manager e, em seguida, fornenciá-lo para ser instalado na loja de certificados dos seus clientes de acesso remoto. Para instruções, vá a:

* [Cmdlet certificado de importação](/powershell/module/pkiclient/import-certificate)

**Q:** Os meus dados estão protegidos se o serviço StorSimple Device Manager estiver comprometido?

**A:** Os dados de configuração do serviço são sempre encriptados com a sua chave pública quando o vê num navegador web. Como o serviço não tem acesso à chave privada, o serviço não poderá ver quaisquer dados. Se o serviço StorSimple Device Manager estiver comprometido, não há impacto, uma vez que não existem chaves armazenadas no serviço StorSimple Device Manager.

**Q:** Se alguém tiver acesso ao certificado de encriptação de dados, os meus dados serão comprometidos?

**A:** O Microsoft Azure armazena a chave de encriptação de dados do cliente (ficheiro.pfx) num formato encriptado. Como o ficheiro .pfx está encriptado e o serviço StorSimple não tem a chave de encriptação de dados de serviço para desencriptar o ficheiro .pfx, simplesmente ter acesso ao ficheiro .pfx não irá expor nenhum segredo.

**Q:** O que acontece se uma entidade governamental pedir à Microsoft os meus dados?

**A:** Como todos os dados são encriptados no serviço e a chave privada é mantida com o dispositivo, a entidade governamental deve pedir os dados ao cliente.



## <a name="next-steps"></a>Passos seguintes

[Coloque o seu dispositivo StorSimple](storsimple-8000-deployment-walkthrough-u2.md).