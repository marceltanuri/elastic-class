---
marp: true
theme: default
paginate: true
header: "Elasticsearch e Kibana para Desenvolvedores Backend Meetup - ADA | MELI 1321 + 1322"
footer: "By Marcel Tanuri"
style: |
  section {
    background-color: #1e1e1e;
    color: white;
  }
---

# Elasticsearch e Kibana
## Para Desenvolvedores Backend com Spring Boot

---

# Objetivo
- Entender o que são Elasticsearch e Kibana
- Aprender como configurá-los para monitorar aplicações Spring Boot
- Explorar outras possibilidades de uso

---

# O que é Observabilidade?
- Capacidade de monitorar e entender o comportamento de uma aplicação
- Inclui:
  - Logs
  - Métricas
  - Rastreamento (Tracing)

---

# Elasticsearch
- Banco de dados NoSQL orientado a documentos
- Ideal para busca e análise de grandes volumes de dados
- Armazena dados em índices e documentos
- Pode operar em cluster, garantindo alta disponibilidade

---

# Kibana
- Interface gráfica para visualização dos dados do Elasticsearch
- Permite criar dashboards e explorar logs e métricas
- Oferece visualização em tempo real e filtros avançados

---

# Elastic Stack Completo
- Elasticsearch: Armazena e analisa dados
- Kibana: Visualiza e explora dados
- Logstash: Processa e transforma dados
- Beats: Coleta dados de diversas fontes
- Elastic Agent: Coleta unificada (logs, métricas)
- X-Pack: Segurança, IA e Machine Learning

---

# Visualização do Elastic Stack
![Imagem do Elastic Stack Completo](https://tse2.mm.bing.net/th?id=OIP.7U2iS8pjA_4MzHBw4ti99gHaEU&pid=Api)
- Uma solução integrada para observabilidade, segurança e busca.

---

# Como Elasticsearch e Kibana funcionam juntos?
- Elasticsearch: Armazena e indexa os dados
- Kibana: Visualiza e analisa os dados
- Em um cluster, você pode visualizar logs de várias aplicações em um único painel

---

# Configurando Elasticsearch e Kibana
- Usaremos Docker para facilitar o setup
```bash
docker network create elastic
docker run -d --name elasticsearch --network elastic -p 9200:9200 -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:8.6.2
docker run -d --name kibana --network elastic -p 5601:5601 docker.elastic.co/kibana/kibana:8.6.2
```

---

# Capturando Logs com Spring Boot
- Usaremos o Logback configurado para enviar logs ao Elasticsearch
- Configuração de exemplo:
```xml
<appender name="ELASTIC" class="ch.qos.logback.classic.net.SocketAppender">
    <remoteHost>localhost</remoteHost>
    <port>9200</port>
    <encoder>
        <pattern>%d{yyyy-MM-dd HH:mm:ss} %-5level %logger{36} - %msg%n</pattern>
    </encoder>
</appender>
```
- Os logs podem ser mantidos localmente e enviados para o Elasticsearch simultaneamente

---

# Visualizando Logs no Kibana
- Criar um índice de logs
- Usar filtros para explorar os logs
- Criar dashboards para monitoramento
- Visualizar logs em tempo real

---

# Demonstração Prática
- Configurar Elasticsearch e Kibana
- Enviar logs de uma aplicação Spring Boot
- Visualizar e analisar os logs no Kibana

---

# IA no Elastic Stack
- Elastic AI Assistant (Assistente de IA)
- Elastic Rerank e ELSER (Modelos de IA pré-treinados)
- Detecção de Anomalias e Previsão de Falhas
- AIOps e Automação de Respostas

### 🎥 Vídeo de Demonstração
- [Elastic AI Assistant para Observabilidade (YouTube)](https://www.youtube.com/watch?v=n9Gbp5NFS8s&utm_source=chatgpt.com)

---

# Segurança e Conformidade
- A Elastic protege seus modelos com licenciamento
- Verificação de licença no cluster
- Termos de uso claros para evitar reutilização

---

# Elastic Stack no Mercado
- Reconhecido como Líder no Magic Quadrant da Gartner para Observabilidade
- Amplamente adotado em setores como Finanças, Tecnologia e Saúde
- Flexível e escalável
- [Relatório Gartner - Elastic como Líder em Observabilidade](https://www.elastic.co/resources/observability/analyst-report/gartner-magic-quadrant-observability-platforms)

---

# Perguntas e Respostas Frequentes
**Q:** Os logs da aplicação ficam apenas no Elasticsearch?
**A:** Não, eles também podem ficar no arquivo padrão de logs.

**Q:** O que é um cluster?
**A:** Um conjunto de servidores que trabalham juntos para armazenar e processar dados de forma distribuída.

**Q:** Por que Elasticsearch + Kibana são tão úteis em um cluster?
**A:** Porque permitem armazenar e consultar grandes volumes de dados distribuídos, garantindo alta disponibilidade e desempenho.

**Q:** O Elasticsearch é apenas para logs?
**A:** Não, ele é uma poderosa ferramenta de busca e análise de dados.

---

# Perguntas e Respostas Frequentes
**Q:** Preciso usar Spring Boot para integrar com Elasticsearch?
**A:** Não, você pode integrar com qualquer linguagem que suporte HTTP.

**Q:** O Kibana é pago?
**A:** Existe uma versão gratuita, mas algumas funcionalidades avançadas são pagas.

**Q:** Como garantir que meus logs não ocupem muito espaço?
**A:** Configurar políticas de retenção no Elasticsearch.

**Q:** É obrigatório usar JSON nos logs enviados para o Elasticsearch?
**A:** Não, mas é o formato mais eficiente para indexação.

---

# Perguntas e Respostas Frequentes
**Q:** Como pesquisar por um log específico no Kibana?
**A:** Usando o campo de busca com filtros.

**Q:** Posso visualizar os logs em tempo real no Kibana?
**A:** Sim, usando a visualização de Logs em tempo real.

**Q:** Como garantir que apenas usuários autorizados vejam os dashboards?
**A:** Configurando controle de acesso no Kibana.

---



# Dúvidas?
- Feedback?
