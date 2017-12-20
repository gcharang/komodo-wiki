### Jumblr이란 무엇인가?
- Jumblr는 네이티브 DEX를 이용하는 Komodo 기반의 완전 분산 익명 서비스 (Anonymizer) 입니다.
- Jumblr를 이용하여 공개된 주소에 있는 당신의 BTC와 KMD 펀드를 Mix할 수 있고, 프로세스에서 혼합된, 어떠한 기록도 제거된 익명의 다른 공개된 주소로 펀드를 받을 수 있습니다.


### 프라이버시와 자금의 익명성에 대한 몇 가지 보안 참고:
- Jumblr 주소 (BTC Jumbler 및 KMD Jumbler) 주소는 Private 주소입니다.
- 당신의 Jumblr 주소를 누구와도 공유하지 마세요.
- Jumblr 주소는 당신의 패스워드와 같습니다. 이것들을 안전하게 지키고 숨겨야 합니다.
- 오직 당신만 Jumblr 주소를 알아야 하며 다른 누구도 알아서는 안 됩니다.
- 당신의 Jumblr 주소를 다른 사람에게 공유하면 Jumblr의 이용 목적을 상실하게 되며 당신의 펀드와 트랜젝션은 더 이상 Private 하지 않습니다.


### Jumblr 펀드를 액세스 하는 법 :
당신의 Jumbler 주소에서 쉽게 액세스 할 수 있습니다. 예를 들어 Passphrase로 지갑에 로그인 하였습니다.

```
duck dog cat donkey
```

1. Jumbler 주소 펀드에 액세스하려면, Logout 하십시오.
2. Iguana App을 닫습니다.
3. Iguana App을 다시 시작하십시오.
4. Full Mode 또는 Basilisk Mode에서 Komodo를 시작하십시오.
5. Passphrase 앞에 'jumblr'를 추가하십시오.

	- "jumblr"는 모두 소문자입니다.
	- "jumblr" 뒤에 공백이 있습니다.

6. 예를 들면, Jumbler Addresses에 아래와 같이 액세스 할 수 있습니다:

```
jumblr duck dog cat donkey
```

7. 펀드를 액세스 하기 위해 Jumblr Passphrase로 로그인하여, 다른 지갑 주소처럼 사용할 수 있습니다.


다시 강조하지만, 당신의 JUMBLR 주소를 다른 사람과 공유하지 마십시오.



### Jumblr 이용 :
- 네이티브 모드에서 Komodo 실행
- Passphrase로 로그인
- Jumblr 메뉴로 이동하십시오.
- 당신이 익명화하려는 펀드가 들어있는 코인의 Deposit 주소를 찾으세요. (처음은 KMD 만 지원되며 이후 버전에서 BTC는 지원될 예정입니다)
- Deposit 주소로 펀드를 송금합니다.
- 지갑을 열어 두십시오.
- [중요] 펀드는 100KMD, 1000KMD 및 10,000KMD Lot 크기로 처리됩니다.
- 큰 Lot 사이즈가 먼저 처리되고 그 다음 작은 사이즈가 처리됩니다.

예를 들면, KMD deposit 주소로 1393 KMD를 보냈습니다. 먼저 1000 KMD를 처리 하고 
100 KMD를 처리하고, 
100 KMD를 처리하고, 
100 KMD를 처리합니다.

93 KMD는 deposit 주소에서 계속 대기합니다.

이 프로세스를 완료하기 위해, KMD deposit 주소로 7 KMD를 더 보내야 합니다.

총 합계가 100 KMD이면 KMD Jumblr 주소로 처리됩니다.
