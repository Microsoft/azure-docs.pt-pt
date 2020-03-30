---
title: Segurança da série StorSimple 8000 [ StorSimple 8000 ] Microsoft Docs
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
ms.openlocfilehash: 4598f71f9b611e68f8eb00676138784833c39f32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75891502"
---
# <a name="storsimple-security-and-data-protection"></a>StorSimple segurança e proteção de dados

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Descrição geral

A segurança é uma grande preocupação para quem está a adotar uma nova tecnologia, especialmente quando a tecnologia é utilizada com dados confidenciais ou proprietários. Ao avaliar diferentes tecnologias, deve considerar riscos e custos acrescidos para a proteção de dados. O Microsoft Azure StorSimple fornece uma solução de segurança e privacidade para a proteção de dados, ajudando a garantir:

* **Confidencialidade** – Apenas entidades autorizadas podem ver os seus dados.
* **Integridade** – Apenas entidades autorizadas podem modificar ou eliminar os seus dados.

A solução Microsoft Azure StorSimple consiste em quatro componentes principais que interagem entre si:

* **Serviço StorSimple Device Manager hospedado no Microsoft Azure** – O serviço de gestão que utiliza para configurar e fornecer o dispositivo StorSimple.
* **Dispositivo StorSimple** – Um dispositivo físico instalado no seu datacenter. Todos os anfitriões e clientes que geram dados ligam-se ao dispositivo StorSimple, e o dispositivo gere os dados e move-os para a nuvem Azure conforme apropriado.
* **Clientes/anfitriões ligados ao dispositivo** – Os clientes da sua infraestrutura que se ligam ao dispositivo StorSimple e geram dados que precisam de ser protegidos.
* **Armazenamento na cloud** – A localização na cloud do Azure onde os dados são armazenados.

As seguintes secções descrevem as funcionalidades de segurança StorSimple que ajudam a proteger cada um destes componentes e os dados armazenados neles. Também inclui uma lista de perguntas que poderá ter sobre a segurança Microsoft Azure StorSimple e as respostas correspondentes.

## <a name="storsimple-device-manager-service-protection"></a>Proteção de serviço StorSimple Device Manager

O serviço StorSimple Device Manager é um serviço de gestão hospedado no Microsoft Azure e utilizado para gerir todos os dispositivos StorSimple que a sua organização adquiriu. Pode aceder ao serviço StorSimple Device Manager utilizando as suas credenciais organizacionais para iniciar sessão no portal Azure através de um navegador web.

O acesso ao serviço StorSimple Device Manager requer que a sua organização tenha uma subscrição Azure que inclua o StorSimple. A subscrição controla as funcionalidades a que pode aceder no portal do Azure. Se a sua organização não tiver uma subscrição Azure e quiser saber mais sobre eles, consulte o [Signe para o Azure como uma organização.](../active-directory/fundamentals/sign-up-organization.md)

Uma vez que o serviço StorSimple Device Manager está hospedado no Azure, está protegido pelas funcionalidades de segurança Azure. Para obter mais informações sobre as funcionalidades de segurança fornecidas pelo Microsoft Azure, aceda ao [Centro de Fidedignidade do Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/).

## <a name="storsimple-device-protection"></a>Proteção do dispositivo StorSimple

O dispositivo StorSimple é um dispositivo de armazenamento híbrido no local que contém unidades de estado sólido (SSDs) e discos rígidos (HDDs), juntamente com controladores redundantes e capacidades automáticas de failover. Os controladores gerem o tiering de armazenamento, colocando dados atualmente utilizados (ou quentes) no armazenamento local (no dispositivo StorSimple ou nos servidores no local), enquanto movem dados menos utilizados para a nuvem.

Apenas os dispositivos Autorizados StorSimple são autorizados a aderir ao serviço StorSimple Device Manager que criou na sua subscrição Azure. Para autorizar um dispositivo, deve registá-lo com o serviço StorSimple Device Manager, fornecendo a chave de registo de serviço. A chave de registo de serviço é uma chave aleatória de 128 bits gerada no portal Azure.

![Chave de registo de serviço](./media/storsimple-security/ServiceRegistrationKey.png)

Para saber como obter uma chave de registo de serviço, vá ao [Passo 2: Obtenha a chave de registo de serviço](storsimple-8000-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key).

A chave de registo de serviço é uma longa chave que contém mais de 100 caracteres. Pode copiar a chave e guardá-la num ficheiro de texto num local seguro para que possa utilizá-la para autorizar dispositivos adicionais, se necessário. Se a chave de registo de serviço for perdida depois de registar o seu primeiro dispositivo, pode gerar uma nova chave a partir do serviço StorSimple Device Manager. Isto não afetará o funcionamento dos dispositivos existentes.

Depois de registado um dispositivo, utiliza fichas para comunicar com o Microsoft Azure. A chave de registo de serviço não é utilizada após o registo do dispositivo.

> [!NOTE]
> Recomendamos que regeneraa a chave de registo de serviço após cada utilização.


## <a name="protect-your-storsimple-solution-via-passwords"></a>Proteja a sua solução StorSimple através de senhas

As palavras-passe são um aspeto importante da segurança do computador e são usadas extensivamente na solução StorSimple para ajudar a garantir que os seus dados estão acessíveis apenas a utilizadores autorizados. O StorSimple permite-lhe configurar as seguintes palavras-passe:

* Palavra-passe do administrador do dispositivo StorSimple
* Protocolo de Autenticação de Aperto de Mão (CHAP) iniciador e senhas-alvo
* Palavra-passe do Snapshot Manager StorSimple

### <a name="windows-powershell-for-storsimple-and-the-storsimple-device-administrator-password"></a>Windows PowerShell para StorSimple e a palavra-passe do administrador do dispositivo StorSimple

O Windows PowerShell para o StorSimple é uma interface de linha de comando que pode utilizar para gerir o dispositivo StorSimple. O Windows PowerShell para o StorSimple possui funcionalidades que permitem registar o seu dispositivo, configurar a interface de rede no seu dispositivo, instalar certos tipos de atualizações, resolver problemas ao aceder à sessão de suporte e alterar o estado do dispositivo. Pode aceder ao Windows PowerShell para StorSimple conectando-se à consola em série do dispositivo ou utilizando o remo do Windows PowerShell.

O remo PowerShell pode ser feito em HTTPS ou HTTP. Se a gestão remota sobre HTTPS estiver ativada, terá de descarregar o certificado de gestão remota do dispositivo e instalá-lo no cliente remoto. Para mais informações sobre o remo powerShell, vá a [Connect remotamente ao seu dispositivo StorSimple](storsimple-8000-remote-connect.md).

Depois de utilizar o Windows PowerShell para o StorSimple para se ligar ao dispositivo, terá de fornecer a palavra-passe do administrador do dispositivo para iniciar sessão no dispositivo.

![Palavra-passe do administrador do dispositivo](./media/storsimple-security/DeviceAdminPW.png)

Tenha em mente as seguintes boas práticas:

* A gestão remota é desligada por defeito. Pode utilizar o serviço StorSimple Device Manager para o ativar. Como uma boa prática de segurança, o acesso remoto só deve ser ativado durante o período de tempo que é realmente necessário.
* Se alterar a palavra-passe, certifique-se de notificar todos os utilizadores de acesso remoto para que não experimentem uma perda inesperada de conectividade.
* O serviço StorSimple Device Manager não consegue recuperar as senhas existentes: só pode redefini-las. Recomendamos que guarde todas as palavras-passe num local seguro para que não tenha de redefinir uma palavra-passe se for esquecida. Se precisar de redefinir uma palavra-passe, certifique-se de notificar todos os utilizadores antes de a redefinir.

Pode aceder à interface Do Windows PowerShell utilizando uma ligação em série ao dispositivo. Também pode acessá-lo remotamente utilizando http ou HTTPS, que fornecem segurança adicional. HTTPS fornece um nível de segurança mais elevado do que uma ligação em série ou HTTP. No entanto, para utilizar https, tem primeiro de instalar um certificado no computador cliente que aceda ao dispositivo. Pode descarregar o certificado de acesso remoto a partir da página de configuração do dispositivo no serviço StorSimple Device Manager. Se o certificado de acesso remoto for perdido, deve descarregar um novo certificado e propagar-o a todos os clientes autorizados a utilizar a gestão remota.

### <a name="challenge-handshake-authentication-protocol-chap-initiator-and-target-passwords"></a>Protocolo de Autenticação de Aperto de Mão (CHAP) iniciador e senhas-alvo

O CHAP é um esquema de autenticação utilizado pelo dispositivo StorSimple para validar a identidade dos clientes remotos. A verificação baseia-se numa palavra-passe partilhada. O CHAP pode ser unidirecional ou mútuo (bidirecional). Com CHAP de ida, o alvo (o dispositivo StorSimple) autentica um iniciador (hospedeiro). O CHAP mútuo ou invertido exige que o alvo autenticar o iniciador e, em seguida, o iniciador autenticar o alvo. O seu StorSimple pode ser configurado para utilizar qualquer um dos métodos.

Esteja atento ao seguinte quando configurar chap:

* O nome de utilizador CHAP deve conter menos de 233 caracteres.
* A palavra-passe CHAP deve estar entre 12 e 16 caracteres. Tentar utilizar um nome de utilizador ou palavra-passe mais longo resultará numa falha de autenticação no anfitrião do Windows.
* Não é possível utilizar a mesma palavra-passe tanto para o iniciador CHAP como para o alvo CHAP.
* Depois de definir a palavra-passe, pode ser alterada, mas não pode ser recuperada. Se a palavra-passe for alterada, certifique-se de notificar todos os utilizadores de acesso remoto para que possam ligar-se com sucesso ao dispositivo StorSimple.

Para mais informações sobre o CHAP e como configurá-lo para a sua solução StorSimple, vá ao [Configure CHAP para o seu dispositivo StorSimple](storsimple-8000-configure-chap.md).

### <a name="storsimple-snapshot-manager-password"></a>Palavra-passe do Snapshot Manager StorSimple

StorSimple Snapshot Manager é um snap-in da Consola de Gestão da Microsoft (MMC) que utiliza grupos de volume e o Serviço de Cópia sonante do Volume Windows para gerar backups consistentes com aplicações. Além disso, pode utilizar o StorSimple Snapshot Manager para criar horários de backup e clonar ou restaurar volumes.

Quando configurar um dispositivo para utilizar o StorSimple Snapshot Manager, será necessário fornecer a palavra-passe do StorSimple Snapshot Manager. Esta palavra-passe é primeiramente definida no Windows PowerShell para StorSimple durante o registo. A palavra-passe também pode ser definida e alterada do serviço StorSimple Device Manager. Esta palavra-passe autentica o dispositivo com o StorSimple Snapshot Manager.

![Palavra-passe do Snapshot Manager StorSimple](./media/storsimple-security/SnapshotMgrPassword.png)

A palavra-passe do StorSimple Snapshot Manager deve ter 14 a 15 caracteres e deve conter 3 ou mais de uma combinação de caracteres maiúsculos, minúsculos, numéricos e especiais. Depois de definir a palavra-passe do StorSimple Snapshot Manager, pode ser alterada, mas não pode ser recuperada. Se alterar a palavra-passe, certifique-se de notificar todos os utilizadores remotos.

Para mais informações sobre o StorSimple Snapshot Manager, vá ao [What is StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="password-best-practices"></a>Melhores práticas de palavra-passe

Recomendamos que utilize as seguintes diretrizes para ajudar a garantir que as palavras-passe StorSimple são fortes e bem protegidas:

* Mude as suas palavras-passe de três em três meses. A alteração das palavras-passe é aplicada anualmente.
* Use senhas fortes. Para mais informações, aceda à [Create mais fortes palavras-passe e proteja-as](https://cloudblogs.microsoft.com/microsoftsecure/2014/08/25/create-stronger-passwords-and-protect-them/).
* Utilize sempre senhas diferentes para diferentes mecanismos de acesso; cada uma das palavras-passe que especifica deve ser única.
* Não partilhe palavras-passe com quem não esteja autorizado a aceder ao dispositivo StorSimple.
* Não fale de uma palavra-passe na frente dos outros nem indique o formato de uma palavra-passe.
* Se suspeitar que uma conta ou senha foi comprometida, reporte o incidente ao seu departamento de segurança de informação.
* Trate todas as palavras-passe como informações confidenciais e confidenciais. 

## <a name="storsimple-data-protection"></a>Proteção de dados StorSimple

Esta secção descreve as funcionalidades de segurança StorSimple que protegem os dados em trânsito e dados armazenados.

Conforme descrito noutras secções, as palavras-passe são usadas para autorizar e autenticar os utilizadores antes de poderem ter acesso à sua solução StorSimple. Outra consideração de segurança é proteger os dados de utilizadores não autorizados enquanto estes estão a ser transferidos entre sistemas de armazenamento e enquanto estão a ser armazenados. As seguintes secções descrevem as funcionalidades de proteção de dados fornecidas com o StorSimple.

> [!NOTE]
> A desduplicação fornece proteção adicional para os dados armazenados no dispositivo StorSimple e no armazenamento do Microsoft Azure. Quando os dados são desduplicados, os objetos de dados são armazenados separadamente dos metadados utilizados para mapear e aceder aos mesmos: não existe um contexto disponível ao nível de armazenamento para reconstruir os dados com base na estrutura de volume, sistema de ficheiros ou nome de ficheiro.


## <a name="protect-data-flowing-through-the-service"></a>Proteja os dados que fluem através do serviço

O principal objetivo do serviço StorSimple Device Manager é gerir e configurar o dispositivo StorSimple. O serviço StorSimple Device Manager funciona no Microsoft Azure. Utiliza o portal Azure para introduzir dados de configuração do dispositivo e, em seguida, o Microsoft Azure utiliza o serviço StorSimple Device Manager para enviar os dados para o dispositivo. A StorSimple utiliza um sistema de pares de chaves assimétricas para ajudar a garantir que um compromisso do serviço Azure não resultará num compromisso de informação armazenada.

![Encriptação de dados em voo](./media/storsimple-security/DataEncryption.png)

O sistema de chaves assimétricas ajuda a proteger os dados que fluem através do serviço da seguinte forma:

1. Um certificado de encriptação de dados que utiliza um par de chaves públicas e privadas assimétricas é gerado no dispositivo e é usado para proteger os dados. As teclas são geradas quando o primeiro dispositivo está registado.
2. As chaves do certificado de encriptação de dados são exportadas para um ficheiro Personal Information Exchange (.pfx) protegido pela chave de encriptação de dados de serviço, que é uma forte chave de 128 bits que é gerada aleatoriamente pelo primeiro dispositivo durante o registo.
3. A chave pública do certificado é disponibilizada de forma segura para o serviço StorSimple Device Manager, e a chave privada permanece com o dispositivo.
4. Os dados que entram no serviço são encriptados utilizando a chave pública e desencriptados utilizando a chave privada armazenada no dispositivo, garantindo que o serviço Azure não pode desencriptar os dados que fluem para o dispositivo.

A chave de encriptação de dados de serviço é gerada apenas no primeiro dispositivo registado com o serviço. Todos os dispositivos subsequentes registados no serviço devem utilizar a mesma chave de encriptação de dados de serviço.

> [!IMPORTANT]
> É muito importante fazer uma cópia da chave de encriptação de dados de serviço e guardá-la num local seguro. Uma cópia da chave de encriptação de dados de serviço deve ser armazenada de modo a que possa ser acedida por uma pessoa autorizada e possa ser facilmente comunicada ao administrador do dispositivo.
> 
> Se a chave de encriptação de dados de serviço for perdida, uma pessoa de suporte da Microsoft pode ajudá-lo a recuperá-la desde que tenha pelo menos um dispositivo num estado online. Recomendamos que altere a chave de encriptação de dados do serviço após a sua recuperação.

Para alterar a chave de encriptação de dados do serviço e o correspondente certificado de encriptação de dados, siga os passos na Alteração da chave de encriptação de [dados do serviço para o seu serviço StorSimple Device Manager](storsimple-8000-manage-service.md#change-the-service-data-encryption-key). A alteração das teclas de encriptação requer que todos os dispositivos sejam atualizados com a nova chave. Por isso, recomendamos que altere a chave quando todos os dispositivos estiverem online. Se os dispositivos estiverem offline, as chaves podem ser alteradas num momento diferente. Os dispositivos com chaves desatualizadas ainda poderão executar cópias de segurança, mas não poderão restaurar os dados até que a chave seja atualizada.

A chave de encriptação de dados de serviço e o certificado de encriptação de dados não expiram. No entanto, recomendamos que altere anualmente a chave de encriptação de dados de serviços para ajudar a prevenir o compromisso chave.

## <a name="protect-data-at-rest"></a>Proteger os dados inativos

O dispositivo StorSimple gere os dados armazenando-os em níveis locais e na nuvem, dependendo da frequência de utilização. Todas as máquinas hospedeiras que estejam ligadas ao dispositivo enviam dados para o dispositivo, que depois transfere os dados para a nuvem, conforme apropriado. Os dados são transferidos do dispositivo para a nuvem de forma segura através da Internet. Cada dispositivo tem um alvo iSCSI que superfícies de todos os volumes partilhados nesse dispositivo. Todos os dados são encriptados antes de serem enviados para armazenamento em nuvem. 

![Chave de encriptação de armazenamento em nuvem](./media/storsimple-security/CloudStorageEncryption.png)

Para ajudar a garantir a segurança e integridade dos dados transferidos para a nuvem, o StorSimple permite-lhe definir as chaves de encriptação de armazenamento em nuvem da seguinte forma:

* Especifica a chave de encriptação de armazenamento em nuvem quando cria um recipiente de volume. A chave não pode ser modificada ou adicionada mais tarde.
* Todos os volumes de um contentor de volume partilham a mesma chave de encriptação. Se quiser uma forma diferente de encriptação para um volume específico, recomendamos que crie um novo recipiente de volume para alojar esse volume.
* Quando introduz a chave de encriptação de armazenamento em nuvem no serviço StorSimple Device Manager, a chave é encriptada utilizando a parte pública da chave de encriptação de dados de serviço e depois enviada para o dispositivo.
* A chave de encriptação de armazenamento em nuvem não é armazenada em nenhum lugar do serviço e é conhecida apenas pelo dispositivo.
* Especificar uma chave de encriptação de armazenamento em nuvem é opcional. Pode enviar dados encriptados no hospedeiro para o dispositivo.

### <a name="additional-security-best-practices"></a>Boas práticas adicionais de segurança

* Tráfego dividido: isolar o iSCSI SAN do tráfego de utilizadores numa LAN corporativa, implantando uma rede totalmente separada e utilizando VLANs onde o isolamento físico não é uma opção. Uma rede dedicada para armazenamento iSCSI garantirá a segurança e o desempenho dos seus dados críticos de negócio. A mistura de armazenamento e tráfego de utilizadores sobre uma LAN corporativa não é recomendada e pode aumentar a latência e causar falhas de rede.
* Para a segurança da rede do lado do anfitrião, utilize interfaces de rede que suportem o Motor de Descarga TCP/IP (TOE). O TOE reduz a carga de CPU processando o TCP no adaptador de rede.

## <a name="protect-data-via-storage-accounts"></a>Proteja os dados através de contas de armazenamento

Cada subscrição do Microsoft Azure pode criar uma ou mais contas de armazenamento. (Uma conta de armazenamento fornece um espaço de nome único para trabalhar com dados armazenados na nuvem Azure.) O acesso a uma conta de armazenamento é controlado pelas chaves de subscrição e acesso associadas a essa conta de armazenamento.

Ao criar uma conta de armazenamento, o Microsoft Azure gera duas chaves de acesso de armazenamento de 512 bits, uma das quais é usada para autenticação quando o dispositivo StorSimple acede à conta de armazenamento. Note que apenas uma destas chaves está a ser utilizada. A outra tecla é mantida na reserva, permitindo-lhe rodar as teclas periodicamente. Para rodar as teclas, torna a chave secundária ativa e, em seguida, elimina a tecla principal. Em seguida, pode criar uma nova chave para utilização durante a próxima rotação. (Por razões de segurança, muitos centros de dados requerem rotação chave.)

Recomendamos que siga estas boas práticas para a rotação da chave:

* Deve rodar regularmente as chaves da conta de armazenamento para ajudar a garantir que a sua conta de armazenamento não é acedida por utilizadores não autorizados.
* Periodicamente, o seu administrador Azure deve alterar ou regenerar a chave primária ou secundária utilizando a secção de armazenamento do portal Azure para aceder diretamente à conta de armazenamento.

## <a name="protect-data-via-encryption"></a>Proteja os dados através da encriptação

O StorSimple utiliza os seguintes algoritmos de encriptação para proteger os dados armazenados ou viajando entre os componentes da sua solução StorSimple.

| Algoritmo | Comprimento da chave | Protocolos/aplicações/comentários |
| --- | --- | --- |
| RSA |2048 |RSA PKCS 1 v1.5 é utilizado pelo portal Azure para encriptar dados de configuração que são enviados para o dispositivo: por exemplo, credenciais de conta de armazenamento, configuração do dispositivo StorSimple e chaves de encriptação de armazenamento em nuvem. |
| AES |256 |A EsA com CBC é usada para encriptar a parte pública da chave de encriptação de dados de serviço antes de ser enviada para o portal Azure a partir do dispositivo StorSimple. Também é utilizado pelo dispositivo StorSimple para encriptar dados antes de os dados serem enviados para a conta de armazenamento na nuvem. |

## <a name="storsimple-cloud-appliance-security"></a>Segurança do eletrodoméstico StorSimple Cloud

[!INCLUDE [storsimple Cloud Appliance security](../../includes/storsimple-virtual-device-security.md)]

## <a name="managing-personal-information"></a>Gestão de informações pessoais

O StorSimple Device Manager para séries físicas e virtuais recolhe informações pessoais nas seguintes instâncias-chave:

- Alerte as definições do utilizador sempre que o endereço de e-mail dos utilizadores esteja configurado. Esta informação pode ser vista e apurada pelo administrador. Isto aplica-se tanto aos dispositivos da série StorSimple 8000 como aos Matrizes Virtuais StorSimple.
  * Para visualizar e limpar as definições da série StorSimple 8000, siga os passos em [View e gerencie os alertas StorSimple](storsimple-8000-manage-alerts.md#configure-alert-settings)
  * Para visualizar e limpar as definições para StorSimple Virtual Array, siga os passos em [View e gerencie os alertas StorSimple](storsimple-virtual-array-manage-alerts.md#configure-alert-settings)
- Utilizadores que possam aceder aos dados residentes nas ações. Uma lista de utilizadores que podem aceder aos dados da partilha é apresentada e pode ser visualizada. Esta lista também é eliminada quando as ações são eliminadas. Isto aplica-se apenas às Matrizes Virtuais StorSimple.
  * Para ver a lista de utilizadores que podem aceder ou apagar uma ação, siga os passos em [Gerir ações no StorSimple Virtual Array](storsimple-virtual-array-manage-shares.md)

Para obter mais informações, reveja a política de privacidade da Microsoft no [Centro de Fidedignidade](https://www.microsoft.com/trustcenter).

## <a name="frequently-asked-questions-faq"></a>Perguntas Mais Frequentes (FAQ)

Seguem-se algumas perguntas e respostas sobre segurança e O Microsoft Azure StorSimple.

**Q:** O meu serviço está comprometido. Quais serão os meus próximos passos?

**A:** Deve alterar imediatamente a chave de encriptação de dados do serviço e as chaves da conta de armazenamento para a conta de armazenamento que está a ser utilizada para o tiering de dados. Para instruções, vá a:

* [Alterar a chave de encriptação de dados do serviço](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Rotação chave das contas de armazenamento](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**Q:** Tenho um novo dispositivo StorSimple que está a pedir a chave de registo de serviço. Como posso recuperá-lo?

**A:** Esta chave foi criada quando criou o serviço StorSimple Device Manager. Quando utilizar o serviço StorSimple Device Manager para se ligar ao dispositivo, pode utilizar a página de início rápida do serviço para visualizar ou regenerar a chave de registo de serviço. A criação de uma nova chave de registo de serviços não afetará os dispositivos registados existentes. Para instruções, vá a:

* [Ver ou regenerar a chave de registo de serviço](storsimple-8000-manage-service.md#regenerate-the-service-registration-key)

**Q:** Perdi a chave de encriptação de dados de serviço. O que posso fazer?

**A:** Contacte o Suporte da Microsoft. Podem iniciar sessão de suporte no seu dispositivo e ajudá-lo a recuperar a chave (desde que pelo menos um dispositivo esteja online). Imediatamente após a obtenção da chave de encriptação de dados de serviço, deve troque-a para garantir que a nova chave é conhecida apenas por si. Para instruções, vá a:

* [Alterar a chave de encriptação de dados do serviço](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)

**Q:**  Autorizei um dispositivo para uma alteração da chave de encriptação de dados de serviço, mas não iniciei o processo de alteração da chave. O que devo fazer?

**A:** Se o período de tempo de saída tiver expirado, terá de reautorizar o dispositivo para a alteração da chave de encriptação de dados do serviço e iniciar o processo novamente.

**Q:**  Mudei a chave de encriptação de dados de serviço, mas não consegui atualizar os outros dispositivos dentro de 4 horas. Tenho que começar de novo?

**A:** O período de 4 horas é apenas para começar a mudança. Depois de iniciar o processo de atualização no dispositivo Autorizado StorSimple, a autorização é válida até que todos os dispositivos sejam atualizados.

**Q:** O nosso administrador StorSimple deixou a empresa. O que devo fazer?

**A:** Altere e reajuste as palavras-passe que permitem o acesso ao dispositivo StorSimple e altere a chave de encriptação de dados de serviço para garantir que as novas informações não são conhecidas por pessoal não autorizado. Para instruções, vá a:

* [Utilize o serviço StorSimple Device Manager para alterar as suas palavras-passe storsimple](storsimple-8000-change-passwords.md)
* [Alterar a chave de encriptação de dados do serviço](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Configure CHAP para o seu dispositivo StorSimple](storsimple-8000-configure-chap.md)

**Q:** Quero fornecer a senha do StorSimple Snapshot Manager a um anfitrião que está a ligar-se ao dispositivo StorSimple, mas a palavra-passe não está disponível. Que posso eu fazer?

**A:** Se esqueceu a senha, deve criar uma nova. Em seguida, certifique-se de informar todos os utilizadores existentes que a palavra-passe foi alterada e que devem atualizar os seus clientes para usar a nova palavra-passe. Para instruções, vá a:

* [Alterar a palavra-passe do StorSimple Snapshot Manager](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password)
* [Autenticar um dispositivo](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**Q:** O certificado de acesso remoto ao Windows PowerShell para storSimple foi alterado no dispositivo. Como atualizo os meus clientes de acesso remoto?

**A:** Pode descarregar o novo certificado do serviço StorSimple Device Manager e, em seguida, fornecê-lo para ser instalado na loja de certificados dos seus clientes de acesso remoto. Para instruções, vá a:

* [Cmdlet de certificado de importação](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate)

**Q:** Os meus dados estão protegidos se o serviço StorSimple Device Manager estiver comprometido?

**A:** Os dados de configuração do serviço são sempre encriptados com a sua chave pública quando os vê num navegador web. Como o serviço não tem acesso à chave privada, o serviço não poderá ver quaisquer dados. Se o serviço StorSimple Device Manager estiver comprometido, não há impacto, uma vez que não existem chaves armazenadas no serviço StorSimple Device Manager.

**Q:** Se alguém tiver acesso ao certificado de encriptação de dados, os meus dados serão comprometidos?

**A:** O Microsoft Azure armazena a chave de encriptação de dados do cliente (ficheiro.pfx) num formato encriptado. Uma vez que o ficheiro .pfx está encriptado e o serviço StorSimple não tem a chave de encriptação de dados de serviço para desencriptar o ficheiro .pfx, simplesmente ter acesso ao ficheiro .pfx não vai expor quaisquer segredos.

**Q:** O que acontece se uma entidade governamental pedir os meus dados à Microsoft?

**A:** Como todos os dados estão encriptados no serviço e a chave privada é mantida com o dispositivo, a entidade governamental deve pedir ao cliente os dados.



## <a name="next-steps"></a>Passos seguintes

[Implemente o seu dispositivo StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

