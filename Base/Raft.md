## Raft
aliases: 
	- "Raft"

---

# Raft

**Raft** был предложен в 2014 году (дипломная работа Диего Онгаро и Джона Остерhout) как альтернатива Paxos, ориентированная на «простоту понимания» и более прозрачное разделение стадий. Быстро стал популярен (etcd, Consul, TiDB, RethinkDB и многие другие используют Raft).

## Основные особенности

1. **Выделенный лидер** (Leader) — всегда ровно один (при корректном функционировании). Остальные — Follower’ы (и, возможно, Candidate при выборах).
2. **Термы (terms)** — аналог номеров раундов. Каждый «сезон» лидера имеет свой номер term, и, если лидер теряет большинство, наступает новый term.
3. **Голосование** — если узёл подозревает, что лидера нет (или просрочен heartbeat), он становится Candidate и запрашивает голоса у остальных. Те голосуют, если ещё не поддержали лидера с большим term. Когда Candidate получает большинство голосов, он становится Leader в этом term.
4. **Лог репликации** — лидер при получении новой команды (записи) добавляет её в лог локально с индексом и рассылает по всем Follower’ам. Когда большинство Follower’ов подтвердили, что записали её на диск, лидер объявляет запись «зафиксированной» (committed).
5. **Безопасность** — если запись считается «зафиксированной», никакой будущий лидер не может её отменить. То есть Raft гарантирует, что консенсусное решение по индексу журнала не «откатится» при выборах.

## Чтения и Read Index

- Raft обеспечивает линеаризуемые записи (write). Для чтений бывает два варианта:
    - **Синхронный**: дождаться подтверждения, что узёл, к которому обратились, в курсе последних зафиксированных записей. Это требует либо отправить запрос лидеру, либо использовать механизм Read Index (лидер запрашивает большинство, что он ещё «жив»).
    - **Асимметричный**: читать локально (как follower), но это может нарушить линеаризуемость, если данные отстают.

## Плюсы Raft

- Считается чуть проще в реализации, чем Multi-Paxos. Чётко разделены этапы выборов лидера, репликации лога и механизма «term».
- Большая популярность открытых библиотек/реализаций (go-raft, etcd-raft, HashiCorp Raft, Patroni и др.).

---
Tags: #highLoad #develop #coherence  #consensus
Author: [[]]
Related: [[Консенсусные протоколы]]
URL: -- 