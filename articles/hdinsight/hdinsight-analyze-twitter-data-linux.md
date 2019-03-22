---
title: Analisar dados do Twitter com o Apache Hive - Azure HDInsight
description: Saiba como utilizar o Apache Hive e Apache Hadoop no HDInsight para transformar dados não processados do TWitter para uma tabela do Hive pesquisável.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 600b18997ced80a63b7e86f66bc447ddd7932687
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310749"
---
# <a name="analyze-twitter-data-using-apache-hive-and-apache-hadoop-on-hdinsight"></a>Analisar dados do Twitter com o Apache Hive e Apache Hadoop no HDInsight

Aprenda a usar [Apache Hive](https://hive.apache.org/) para processar dados do Twitter. O resultado é uma lista de utilizadores do Twitter que enviou os maioria dos tweets que contêm uma determinada palavra.

> [!IMPORTANT]  
> Os passos neste documento foram testados no HDInsight 3.6.
>
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

## <a name="get-the-data"></a>Obter os dados

Twitter permite-lhe obter os dados para cada tweet como um documento de JavaScript Object Notation (JSON) através da REST API. [OAuth](https://oauth.net) é necessária para a autenticação para a API.

### <a name="create-a-twitter-application"></a>Criar uma aplicação do Twitter

1. Num browser, inicie sessão no [ https://apps.twitter.com/ ](https://apps.twitter.com/). Clique nas **Inscreva-se agora** ligar se não tiver uma conta do Twitter.

2. Clique em **criar nova aplicação**.

3. Introduza **Name**, **Descrição**, **site**. Pode compor um URL para o **Web site** campo. A tabela seguinte mostra alguns valores de exemplo para utilizar:

   | Campo | Valor |
   |:--- |:--- |
   | Name |MyHDInsightApp |
   | Descrição |MyHDInsightApp |
   | Web site |https:\//www.myhdinsightapp.com |

4. Verifique **Sim, eu Concordo**e, em seguida, clique em **criar a sua aplicação do Twitter**.

5. Clique nas **permissões** separador. A permissão de predefinição está **só de leitura**.

6. Clique nas **chaves e Tokens de acesso** separador.

7. Clique em **criar meu token de acesso**.

8. Clique em **OAuth de teste** no canto superior direito da página.

9. Anote **chave de consumidor**, **segredo de consumidor**, **token de acesso**, e **segredo de token de acesso**.

### <a name="download-tweets"></a>Transferir tweets

O código de Python seguinte transfere 10.000 tweets do Twitter e guardá-los num ficheiro denominado **tweets.txt**.

> [!NOTE]  
> Os seguintes passos são executados no cluster do HDInsight, uma vez que já está instalado o Python.

1. Ligar ao cluster do HDInsight com o SSH:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Utilize os seguintes comandos para instalar [Tweepy](https://www.tweepy.org/), [barra de progresso](https://pypi.python.org/pypi/progressbar/2.2)e outros pacotes necessários:

   ```bash
   sudo apt install python-dev libffi-dev libssl-dev
   sudo apt remove python-openssl
   pip install virtualenv
   mkdir gettweets
   cd gettweets
   virtualenv gettweets
   source gettweets/bin/activate
   pip install tweepy progressbar pyOpenSSL requests[security]
   ```

4. Utilize o seguinte comando para criar um ficheiro denominado **gettweets.py**:

   ```bash
   nano gettweets.py
   ```

5. Utilize o seguinte texto como conteúdo dos **gettweets.py** ficheiro:

   ```python
   #!/usr/bin/python

   from tweepy import Stream, OAuthHandler
   from tweepy.streaming import StreamListener
   from progressbar import ProgressBar, Percentage, Bar
   import json
   import sys

   #Twitter app information
   consumer_secret='Your consumer secret'
   consumer_key='Your consumer key'
   access_token='Your access token'
   access_token_secret='Your access token secret'

   #The number of tweets we want to get
   max_tweets=10000

   #Create the listener class that receives and saves tweets
   class listener(StreamListener):
       #On init, set the counter to zero and create a progress bar
       def __init__(self, api=None):
           self.num_tweets = 0
           self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()

       #When data is received, do this
       def on_data(self, data):
           #Append the tweet to the 'tweets.txt' file
           with open('tweets.txt', 'a') as tweet_file:
               tweet_file.write(data)
               #Increment the number of tweets
               self.num_tweets += 1
               #Check to see if we have hit max_tweets and exit if so
               if self.num_tweets >= max_tweets:
                   self.pbar.finish()
                   sys.exit(0)
               else:
                   #increment the progress bar
                   self.pbar.update(self.num_tweets)
           return True

       #Handle any errors that may occur
       def on_error(self, status):
           print status

   #Get the OAuth token
   auth = OAuthHandler(consumer_key, consumer_secret)
   auth.set_access_token(access_token, access_token_secret)
   #Use the listener class for stream processing
   twitterStream = Stream(auth, listener())
   #Filter for these topics
   twitterStream.filter(track=["azure","cloud","hdinsight"])
   ```

    > [!IMPORTANT]  
    > Substitua o texto de marcador de posição para os seguintes itens com as informações do seu aplicativo do twitter:
    >
    > * `consumer_secret`
    > * `consumer_key`
    > * `access_token`
    > * `access_token_secret`

    > [!TIP]  
    > Ajuste o filtro de tópicos sobre a última linha para controlar as palavras-chave populares. Utilizar palavras-chave populares no momento em que executa o script permite que mais rápida captura de dados.

6. Uso **Ctrl + X**, em seguida, **Y** para guardar o ficheiro.

7. Utilize o seguinte comando para executar o ficheiro e transferir tweets:

    ```bash
    python gettweets.py
    ```

    É apresentado um indicador de progresso. Isso será contado até 100% à medida que são transferidos os tweets.

   > [!NOTE]  
   > Se está a demorar muito tempo para a barra de progresso avançar, deve alterar o filtro para controlar os tópicos em destaque. Quando existem muitos tweets sobre o tópico no seu filtro, pode obter rapidamente os 10000 tweets necessário.

### <a name="upload-the-data"></a>Carregar os dados

Para carregar os dados para o armazenamento do HDInsight, utilize os seguintes comandos:

```bash
hdfs dfs -mkdir -p /tutorials/twitter/data
hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt
```

Estes comandos armazenam os dados numa localização à qual podem aceder a todos os nós do cluster.

## <a name="run-the-hiveql-job"></a>Executar a tarefa de HiveQL

1. Utilize o seguinte comando para criar um ficheiro que contém [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) instruções:

   ```bash
   nano twitter.hql
   ```

    Utilize o seguinte texto como o conteúdo do ficheiro:

   ```hiveql
   set hive.exec.dynamic.partition = true;
   set hive.exec.dynamic.partition.mode = nonstrict;
   -- Drop table, if it exists
   DROP TABLE tweets_raw;
   -- Create it, pointing toward the tweets logged from Twitter
   CREATE EXTERNAL TABLE tweets_raw (
       json_response STRING
   )
   STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
   -- Drop and recreate the destination table
   DROP TABLE tweets;
   CREATE TABLE tweets
   (
       id BIGINT,
       created_at STRING,
       created_at_date STRING,
       created_at_year STRING,
       created_at_month STRING,
       created_at_day STRING,
       created_at_time STRING,
       in_reply_to_user_id_str STRING,
       text STRING,
       contributors STRING,
       retweeted STRING,
       truncated STRING,
       coordinates STRING,
       source STRING,
       retweet_count INT,
       url STRING,
       hashtags array<STRING>,
       user_mentions array<STRING>,
       first_hashtag STRING,
       first_user_mention STRING,
       screen_name STRING,
       name STRING,
       followers_count INT,
       listed_count INT,
       friends_count INT,
       lang STRING,
       user_location STRING,
       time_zone STRING,
       profile_image_url STRING,
       json_response STRING
   );
   -- Select tweets from the imported data, parse the JSON,
   -- and insert into the tweets table
   FROM tweets_raw
   INSERT OVERWRITE TABLE tweets
   SELECT
       cast(get_json_object(json_response, '$.id_str') as BIGINT),
       get_json_object(json_response, '$.created_at'),
       concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
       substr (get_json_object(json_response, '$.created_at'),27,4)),
       substr (get_json_object(json_response, '$.created_at'),27,4),
       case substr (get_json_object(json_response,    '$.created_at'),5,3)
           when "Jan" then "01"
           when "Feb" then "02"
           when "Mar" then "03"
           when "Apr" then "04"
           when "May" then "05"
           when "Jun" then "06"
           when "Jul" then "07"
           when "Aug" then "08"
           when "Sep" then "09"
           when "Oct" then "10"
           when "Nov" then "11"
           when "Dec" then "12" end,
       substr (get_json_object(json_response, '$.created_at'),9,2),
       substr (get_json_object(json_response, '$.created_at'),12,8),
       get_json_object(json_response, '$.in_reply_to_user_id_str'),
       get_json_object(json_response, '$.text'),
       get_json_object(json_response, '$.contributors'),
       get_json_object(json_response, '$.retweeted'),
       get_json_object(json_response, '$.truncated'),
       get_json_object(json_response, '$.coordinates'),
       get_json_object(json_response, '$.source'),
       cast (get_json_object(json_response, '$.retweet_count') as INT),
       get_json_object(json_response, '$.entities.display_url'),
       array(
           trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
       array(
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
       trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
       trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
       get_json_object(json_response, '$.user.screen_name'),
       get_json_object(json_response, '$.user.name'),
       cast (get_json_object(json_response, '$.user.followers_count') as INT),
       cast (get_json_object(json_response, '$.user.listed_count') as INT),
       cast (get_json_object(json_response, '$.user.friends_count') as INT),
       get_json_object(json_response, '$.user.lang'),
       get_json_object(json_response, '$.user.location'),
       get_json_object(json_response, '$.user.time_zone'),
       get_json_object(json_response, '$.user.profile_image_url'),
       json_response
   WHERE (length(json_response) > 500);
   ```

2. Prima **Ctrl + X**, em seguida, prima **Y** para guardar o ficheiro.
3. Utilize o seguinte comando para executar o HiveQL contida no ficheiro:

   ```bash
   beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i twitter.hql
   ```

    Este comando executa o **twitter.hql** ficheiro. Assim que a consulta estiver concluído, verá um `jdbc:hive2//localhost:10001/>` prompt.

4. A partir de linha de comandos beeline, utilize a seguinte consulta para verificar que os dados foram importados:

   ```hiveql
   SELECT name, screen_name, count(1) as cc
   FROM tweets
   WHERE text like "%Azure%"
   GROUP BY name,screen_name
   ORDER BY cc DESC LIMIT 10;
   ```

    Esta consulta retorna um máximo de 10 tweets que contêm a palavra **Azure** no texto da mensagem.

    > [!NOTE]  
    > Se tiver alterado o filtro na `gettweets.py` do script, substitua **Azure** com um dos filtros que utilizou.

## <a name="next-steps"></a>Passos Seguintes

Sabe como transformar um conjunto de dados não estruturado do JSON num estruturados [Apache Hive](https://hive.apache.org/) tabela. Para saber mais sobre o Hive no HDInsight, veja os documentos seguintes:

* [Introdução ao HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Analisar dados de atraso de voo com o HDInsight](hdinsight-analyze-flight-delay-data-linux.md)

[curl]: https://curl.haxx.se
[curl-download]: https://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter
