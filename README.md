# Pinata-Party: Flow-based NFT with IPFS

[Medium Tutorial](https://medium.com/pinata/how-to-create-nfts-like-nba-top-shot-with-flow-and-ipfs-701296944bf)을 따라해보며 발생한 에러를 기록하였습니다.

## 1. Prerequisite: just follow the tutorial
### 1.1. 필요한 cdc와 directory  생성 
```
flow project init
```
- flow.json 생성 완료


### 1.2. Emulator 실행

```
flow project start-emulator
```

- Flow Emulator를 실행할 때마다 새로운 keys 자동생성 (address는 default인 f8d6e0586b0a20c7로 고정)

- 따라서 컨트랙트를 deploy할 때는 Emulator에서 생성된  key로 ``flow.json``의 keys를  specify해야함

* ``flow project init``: 1st try
![test2](https://user-images.githubusercontent.com/70181621/111370493-a9395a00-86db-11eb-8a7a-2e37e51651c3.png)
* ``flow project init``: 2nd try
![test3](https://user-images.githubusercontent.com/70181621/111370498-a9d1f080-86db-11eb-99db-1a91a0e7c582.png)

## 2. Error: mint된 NFT의 metadata를 확인하는 Script 실행 시 

```
flow scripts execute ./scripts/CheckTokenMetadata.cdc
>> Failed to submit executable script: client: rpc error: code = Internal desc = failed to encode value: unsupported value: <nil>, <nil>
```

### 2.1. 상황
- NFT를 정의한 컨트랙트 내부에서 문제 발생
- metadata 객체에 force-unwrap operator(!)를 사용하면 확인에서 에러 발생
- error log on Emulator terminal
![scriptError](https://user-images.githubusercontent.com/70181621/111370503-ab031d80-86db-11eb-86d0-c7a3e8bc6e2e.png)
- error occurred at 49:32
![contract](https://user-images.githubusercontent.com/70181621/111370505-ac344a80-86db-11eb-9634-1be5b30e2ec0.png)

### 2.2. 원인
- 제대로 mint되지 않은 것 
- force-unwrap operator는 해당 값이 nil이면 에러 발생

### 2.3. 해결
- tutorial 중간에 [``flow keys generate``로 private key 만들고 해당 key를 ``flow.json``에 수정하는 과정] 을 통째로 하지 말고
- 초기에 Emulator의 default ``keys``로 설정된 그대로 놔두기

### 2.4. 분석
- Medium tutorial에서 1) 컨트랙트 deploy 2) mint 트랜잭션을 실행한 계정은 같음
- 그러나 각각 다른 key로 서명함 > 계정이 2개 것과 같은 효과 > 제대로 mint되지 않은 것 (deploy와  mint를 다른 계정에서 실행했기때문)
- Flow는 private key -> public key -> address로 대응되는 이더리움과 달리, 하나의 계정 주소에 여러 개의 key를 지정할 수 있음 [(key와 address의 인과관계 없음)](https://www.decentology.com/guides-and-tutorials/blockchain-accounts-flow-vs-ethereum)  
- 튜토리얼 작성자도 이 개념을 헷갈려서 에러가 난건데 아직도 확실하게 모르겠음

