# Hadoop_Simple  
Hadoop-related Basic Knowledge and Word Counting Program

## Contents
1. [Using](#using)
2. [Project Order](#project-order)
3. [License](#license)

---------------------------------------------

## Using
1. **OS** - Ubuntu 20.04.1 LTS (VMware)
2. **BackEnd** - Spring Boot (v2.3.9 / Spring Initializer)
3. **BackEnd** - Hive(v3.1.2)
4. **IDE** - IntelliJ(Community / 11.0.9.1 / 2020.3.2)
5. **Etc** - 
----------------------------------------------

## Project Order
<ul>
    <li>Detailed : Can be found in each source code. Comments are written in each source code.</li>
</ul>

1. **하둡의 탄생과 생태계의 활용**  
    1. Hadoop
        1. 매우 많은 데이터 => 정형 + 비정형(빅데이터)  
        2. 하둡은 비정형 데이터를 포함한 빅데이터를 다루기 위한 가장 적절한 플랫폼  
        3. 하둡 에코 시스템 - Governance, Finance, Banking, Insurance, Healthcare 등 전반적으로 사용  
        4. 하둡은 소프트웨어 플랫폼 : 소프트웨어(SA) + 인프라(TA) 지식 필요  
    2. 하둡 탄생
        1. Lucene : 인덱싱 라이브러리, 검색 라이브러리  
        2. Nutch : 웹 검색엔진 (<- GFS(Google File System), Map Reduce (큰 데이터 병렬 분산처리 하기 위한 알고리즘) <- Lucene)  
        3. Hadoop : 빅데이터 처리
    3. |Google| Hadoop|
        |:-------:|:-------|
        |Google File System|Hadoop Distributed File System|
        |Goolgle Map Reduce|Hadoop Map Reduce|
        |Google Bigtable|HBase(NoSQL) 분산 데이터베이스 (RDS-공유스토리지(SAN. NAS), 저장량 MANY, 리소스 MANY => 디스크 I/O DOWN)|
        |Google sawzall|Hive(sql 같은 언어로 하둡데이터를 Handling 하기 위해 사용 (But. 정형데이터만) / 비정형, 반정형 -> Map Reduce), Pig(Map Reduce를 사용하지 않고 분산 파일 시스템 데이터를 처리)|
        |Google chubby|Zookeeper|
        
    4. 주로 활용하는 언어 : Java (Python, R, SQL은 따로 개발이 필요하다.)  
    5. Hadoop 관련
        1. Sqoop : Hadoop <-> RDS, 데이터 변환
        2. HCatalog : Hadoop에 저장된 데잍 스키마를 하나의 Catalog service 내에서 관리
        3. Avro -> Serialization, Deserialization 데이터 송수신 할 때, 경량 프레임워크
    6. 기본 Hadoop 생태계
        1. Languages, Libraries, workflow -> Apache Pig, Hive, Mahout
        2. Data Processing, Resource Management -> Hadoop
        3. Metadata Storage -> Hive (DB 쿼리)
        4. Record Storage -> HBase
        5. File Storage -> Hadoop
        6. Coordination -> Zookeeper
    7. 하둡 에코 시스템 : 데이터 저장, 저장한 데이터를 ETL 처리를 통해서 Data Marts를 만들고, Marts를 만드는 과정의 WorkFlow를 관리, 전체 Coordination, Metadata 관리, 전체 환경 모니터링 / 통합  
2. **하둡 설치**
    1. 자바(JDK 1.8) 버전 확인 및 [설치](https://www.oracle.com/kr/java/te
chnologies/javase/javase-jdk8-
downloads.html)
        ```{.bash}
        ~# java -version
        ```
    2. 하둡(3.3.0) 버전 확인 및 [설치](http://apache.mirror.cdnetworks.com/hadoop/common/hadoop-3.3.0/)
        ```{.bash}
        ~# hadoop version
        ```
    3. 하이브(3.1.2) 버전 확인 및 [설치](http://apache.mirror.cdnetworks.com/hive/hive-3.1.2/)
        ```{.bash}
        ~# hive --version
        ```
    4. 하둡, 하이브 설치
        1. 압축 파일을 특정 디렉토리로 이동시키고 압축을 푼다. (내 기준 디렉토리 = /root/Platform)
        ```{.bash}
        (특정디렉토리)# tar xvfz hadoop-3.3.0.tar.gz
        (특정디렉토리)# tar xvfz apache-hive-3.1.2-bin.tar.gz
        ```
    5. 환경 변수 설정 필요 (개인 OS, 설치 경로, 버전 확인)
        ```{.bash}
        ~# vi .bashrc
        
        export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
        export PATH=$JAVA_HOME/bin/:$PATH
        export CLASS_PATH=$JAVA_HOME:/lib:$CLASS_PATH
        export HADOOP_HOME=/root/Platform/hadoop-3.3.0
        export PATH=$HADOOP_HOME/bin:$PATH
        export PATH=$HADOOP_HOME/sbin:$PATH
        export HIVE_HOME=/root/Platform/apache-hive-3.1.2-bin
        export PATH=$HIVE_HOME/bin:$PATH
        export HADOOP_MAPRED_HOME=$HADOOP_HOME
        export HADOOP_COMMON_HOME=$HADOOP_HOME
        export HADOOP_HDFS_HOME=$HADOOP_HOME
        export YARN_HOME=$HADOOP_HOME
        
        vi 명령행 : wq 로 저장 후
        
        ~# source .bashrc
        ```
    6. 하둡 설정 파일 수정
        ```{.bash}
        # cd /root/Platform/hadoop-3.3.0/etc/hadoop
        ```
        1. hadoop-env.sh 하둡 환경설정 파일
            ```{.bash}
            export HDFS_NAMENODE_USER=root
            export HDFS_DATANODE_USER=root
            export HDFS_SECONDARYNAMENODE_USER=root
            export YARN_RESOURCEMANAGER_USER=root
            export YARN_NODEMANAGER_USER=root
            export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
            export HADOOP_HOME=/root/Platform/hadoop-3.3.0
            export HADOOP_CONF_DIR=${HADOOP_HOME}/etc/hadoop
            ```
        2. core-site.xml 하둡 코어 설정
            ```{.html}
            <configuration>
                    <property>
                            <name>fs.defaultFS</name>
                            <value>hdfs://localhost:9000</value>
                    </property>
            </configuration>
            ```
            추가로 fsimage 파일을 저장할 디렉토리를 지정할 수도 있다.  
            default 위치 = /tmp/hadoop-이름/dfs/name/current
            ```{.html}
            <property>
                    <name>dfs.namenode.name.dir</name>
                    <value>내가 원하는 디렉토리</value>
            </property>
            ```
        3. hdfs-site.xml 하둡 분산파일 시스템에 대한 설정
            ```{.html}
            <configuration>
                    <property>
                            <name>dfs.replication</name>
                            <value>1</value>
                    </property>
            </configuration>
            ```
        4. Hadoop NameNode Format
            ```{.bash}
            (HADOOP_HOME)# bin/hdfs namenode -format
            ```
        5. DFS Daemon 실행 및 확인 (localhost:9870)  
            하둡 분산파일 시스템의 상태를 확인
            ```{.bash}
            (HADOOP_HOME)# sbin/start-dfs.sh
            ```
            <p align="center">
                <img src = "ImageForREADME/dfsDaemon.png", width="100%">
            </p>  
            <p align="center">
                <img src = "ImageForREADME/dfsWeb.png", width="100%">
            </p> 
        6. mapred-site.xml 설정
            ```{.html}
            <configuration>
                    <property>
                            <name>mapreduce.framework.name</name>
                            <value>yarn</value>
                    </property>
                    <property>
                            <name>mapreduce.application.classpath</name>
                            <value>$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/*:$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/lib-examples/*</value>
                    </property>
                     <property>
                            <name>yarn.app.mapreduce.am.env</name>
                            <value>$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/*:$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/lib-examples/*</value>
                    </property>
                    <property>
                            <name>mapreduce.map.env</name>
                            <value>$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/*:$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/lib-examples/*</value>
                    </property>
                    <property>
                            <name>mapreduce.reduce.env</name>
                            <value>$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/*:$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/lib-examples/*</value>
                    </property>
            </configuration>
            ```
        7. yarn-site.xml 설정
            ```{.html}
            <configuration>
                    <property>
                            <name>yarn.nodemanager.aux-services</name>
                            <value>mapreduce_shuffle</value>
                    </property>
                    <property>
                            <name>yarn.nodemanager.env-whitelist</name>
                            <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>
                    </property>
            </configuration>

            ```
        8. YARN Daemon 실행 및 확인 (localhost:8088)  
            어플리케이션 실행 상태 관리 도구
            ```{.bash}
            (HADOOP_HOME)# sbin/start-yarn.sh
            ```
            <p align="center">
                <img src = "ImageForREADME/yarnDaemon.png", width="100%">
            </p>  
            <p align="center">
                <img src = "ImageForREADME/yarnWeb.png", width="100%">
            </p>  
3. **하둡 분산파일시스템의 이해**  
    1. Parellel (CPU 병렬처리 강조), Distributed (Data에 초점)  
    2. 하둡 특성
        1. 수천대 이상의 리눅스 기반 범용 서버들을 하나의 클러스터로 사용
        2. Master(Name Node) - Slave(Data Node) 구조
            1. Master 서버에는 데이터를 저장하지 않는다. 데이터의 위치, 형식을 보관한다.
            2. Slave 서버에 실 데이터 저장, 데이터 조각화(분산), 여러개로 데이터 복사(Hadoop 자체적으로)
        3. Slave에서 Master로 Heart Beat(+Block Report) 주기적으로 전송
    3. 파일을 블록단위로 저장
        1. 하나의 파일을 여러 개의 Block으로 쪼개서 저장
        2. 설정에 의해 하나의 블록은 64MB 또는 128MB
        3. 블록 크기가 128MB보다 적은 경우 실제 크기만큼만 용량 자치
            1. 블록 사이즈가 큰 이유 : 탐색 비용 down, 네트워크를 통해 데이터를 전송하는데 더 많은 시간 할당 가능, 데이터 처리를 빨리할 수 있다.
    4. 하둡 클러스터의 데이터 유실 가능성
        1. 동일 복제본을 저장한 Slave Server(Data Node)들이 거의 동시에 고장나는 경우
        2. 가능성이 거의 없다.
    5. Hadoop 버전에 따른 가장 큰 변화
        1. 1.0 - 2.0 : Master Node(Server) 고장났을 때의 대비
        2. 2.0 - 3.0 : Replication Factor 3의 성능을 거의 2배 용량으로 극복
    6. 블록의 지역성(Locality) : 실제 데이터가 있는 곳에 가서 연산을 먼저 실행
        1. 네트워크를 이용한 데이터 전송 시간 감소
        2. 대용량 데이터 확인을 위한 디스크 탐색 시간 감소
        3. 적절한 단위의 블록 크기를 이용한 cpu 처리 시간 증가
    7. Name Node 역할
        1. 전체 HDFS에 대한 Name Space 관리
        2. Data Node로부터 Block Report를 받음
        3. Data에 대한 Replication 유지를 위한 커맨더 역할 수행
        4. 파일시스템 이미지 파일 관리(fsimage) (snapshot같은 역할)
        5. 파일시스템에 대한 Edit Log 관리
    8. Secondary Name Node(SNN, 보조 네임노드)
        1. NN과 SNN : Active/Stanby 구조가 아니다. fsimage와 edits 파일을 주기적으로 병합
        2. 체크 포인트 : 1시간 주기로 실행, Edits 로그가 일정 사이즈 이상이면 실행
        3. 이슈 사항 : 네임노드가 SPOF, 보조네임노드의 장애상황 감지 툴 없음
    9. Data Node 역할
        1. 물리적으로 로컬 파일시스템에 HDFS 데이터 저장
        2. HDFS에 대한 정보(지식) 없음
        3. 일반적으로 레이드 구성을 하지 않는다. (JBOD, Just Bunch Of Disks)
        4. Block Report : Name Node가 시작될 때, 그리고 주기적으로 로컬파일시스템에 있는 모든 HDFS 블록들을 검사 후 정상적인 블록의 목록을 만들어 NameNode에 전송.
    10. 자바에서 HDFS 파일 읽기 샘플 코드
        ```{.java}
        FileSystem fileSystem = FileSystem.get(conf);
        Path path = new Path("/path/to/file.ext");
        if(!fileSystem.exists(path)){
            System.out.println("File does not exists");
        }
        FSDataInputStream in = fileSystem.open(path);
        int numBytes = 0;
        while((numBytes = in.read(b)) > 0){
            System.out.println((char)numBytes);
            // code to manipulate the data which is read
        }
        in.close();
        out.close();
        fileSystem.close();
        ```
    11. 자바에서 HDFS 파일 쓰기 샘플 코드
        ```{.java}
        FileSystem fileSystem = FileSystem.get(conf);
        // Check if the file already exists
        Path path = new Path("/path/to/file.ext");
        if(fileSystem.exists){
            System.out.println("File "+dest+" already exists");
            return;
        }
        // Create a new file and write data to it
        FSDataOutputStream out = fileSystem.create(path);
        InputStream in = new BufferedInputStream(new FileInputStream(new File(source)));
        byte[] b = new byte[1024];
        int numBytes = 0;
        while((numBytes=in.read(b))>0){
            out.write(b, 0, numBytes);
        }
        // Close all the file descripters
        in.close();
        out.close();
        fileSystem.close();
        ```
    12. 기본 HDFS Commands  
        ```{.bash}
        # hadoop version : 하둡 버전확인
        # hadoop fs -mkdir /tmp : Linux와 동일
        # hadoop fs -ls [-R] / : Linux와 동일
        # hadoop fs -put <local file> <hdfs destination> : 로컬 파일을 하둡으로 넣는다.
        # hadoop fs -copyFromLocal <localsrc> <hdfs destination> : 로컬 파일을 하둡으로 넣는다.
        # hadoop fs -get <src> <localdest> : 하둡의 파일을 로컬로 가져온다.
        # hadoop fs -copyToLocal <hdfs source> <localdst>
        # hadoop fs -cat /path_to_file_in_hdfs : Linux와 동일
        # hadoop fs -mv <src> <dest> : Linux와 동일
        # hadoop fs -cp <src> <dest> : Linux와 동일
        # hadoop fs -moveFromLocal <localsrc> <dest>
        # hadoop fs -tail [-f] <file> : Linux와 동일
        # hadoop fs -rm <path> : Linux와 동일
        # hadoop fs -chown [-R] [owner] [:[group]] <path> : Linux와 동일
        # hadoop fs -chgrp <group> <path> : Linux와 동일
        # hadoop fs -setrep <rep> <path> : 하둡 디렉토리의 replication 설정
        # hadoop fs -du -s /directory : Linux와 동일
        # hadoop fs -df [-h] <path> : Linux와 동일
        # hadoop fs -touchz /directory/filename : Linux와 동일
        # hadoop fs -text <src> : cat과 동일
        # hadoop fs -stat [format] <path> : 저장되어 있는 데이터의 통계 출력
        # hadoop fs -usage <command> : 간단한 사용법
        # hadoop fs -help <command> : 자세한 사용법
        # hadoop fs -checksum <src> : checksum 확인
        # hadoop fs -count [options] <path> : count 확인
        # hadoop fs -find <path> ... <expresion>
        # hadoop fs -getmerge <src> <localdest> : 하둡의 데이터를 merge하고 local로 받을 때 사용
        ```
    13. Rack Awareness : 블록을 저장할 때, 2개의 블록은 같은 랙에, 나머지 하나의 블록은 다른 랙에 저장하도록 구성함. 랙 단위 장애 발생(전원, 스위치)에도 전체 블록이 유실되지 않도록 구성
    14. HDFS 세이프 모드
        1. HDFS의 세이프 모드(Safe Mode)는 데이터 노드를 수정할 수 없는 상태
        2. 세이프 모드가 되면 데이터는 읽기 전용 상태가 되고, 데이터 추가와 수정이 불가능하며 데이터 복제도 일어나지 않는다.
        3. 관리자가 서버 운영 정비를 위해 세이프 모드를 설정할 수 있음.
        4. 네임노드에 문제가 생겨서 정상적인 동작을 할 수 없을 때 자동으로 세이프 모드로 전환
        5. 주로 Missing Block(Replication Block이 하나도 없는 경우)이 발생하는 경우, 혹은 클러스터 재 구동 시 블록 리폴트를 다 받기 전까지 safe mode로 동작
        6. 세이프 모드 상태일 때 파일 복사를 시도하면 에러 메시지 발생
        7. 세이프 모드 명령어 및 복구  
        ```{.bash}
        # hdfs dfsadmin -safemode get
        Safe mode is OFF
        # hdfs dfsadmin -safemode enter
        Safe mode is ON
        # hdfs dfsadmin -safemode leave
        Safe mode is OFF
        ```
        8. HDFS 운영 중 Safe mode에 진입한 경우, 네임노드의 문제인지 데이터노드의 문제인지 파악이 필요하며, fsck 명령으로 HDFS의 무결성을 체크하고, hdfs dfsadmin -report 명령으로 각 데이터 노드의 상태를 확인하여 문제를 확인하고 해결한 후 세이프 모드를 해제 해야 함.
    15. 커럽트 블록 : HDFS는 하트비트를 통해 데이터 블록에 문제가 생기는 것을 감지하고 자동으로 복구를 진행함. 다른 데이터 노드에 복제된 데이터를 가져와서 복구하지만, 모든 복제 블록에 문제가 생겨서 복구하지 못하게 되면 커럽트 상태가 됨. 커럽트 상태의 파일들은 삭제하고, 원본 파일을 다시 HDFS에 올려주어야 함.
    16. HDFS 휴지통 설정 및 명령어
        1. HDFS는 데이터 삭제 시 영구적 데이터를 삭제하지 않도록 휴지통 설정을 할 수 있음.
        2. core-site.xml에 설정
        ```{.html}
        <property>
            <name>fs.trash.interval</name>
            <value>1440</value>
        </property>
        <property>
            <name>fs.trash.checkpoint.interval</name>
            <value>120</value>
        </property>
        ```
        ```{.bash}
        # hadoop fs -expunge : 휴지통 비우기
        # hadoop fs -rm -skipTrash /user/data/file : 휴지통 이용하지 않고 삭제
        ``` 
    17. 네임노드 고가용성
        1. HDFS 고가용성은 이중화된 두대의 서버인 액티브 네임노드와 스탠바이 네임노드를 이용하여 지원.
        2. 액티브 네임노드와 스탠바이 네임노드는 데이터 노드로부터 블록 리포트와 하트비트를 모두 받아서 동일한 메타데이터를 유지하고, 공유 스토리지를 이용하여 에디트파일을 공유
        3. 액티브 네임노드는 네임노드의 역할을 수행하고, 스탠바이 네임노드는 액티브 네임노드와 동일한 메타데이터 정보를 유지하다가, 액티브네임노드에 문제가 발생하면 스탠바이 네임노드가 액티브 네임노드로 동작
        4. 액티브 네임노드에 문제가 발생하는것을 자동으로 확인하는 것이 어렵기 때문에 보통 주키퍼를 이용하여 장애 발생시 자동으로 변경될 수 있도록 구성함
    18. 아파치 주키퍼 : 분산 시스템의 코디네이터
        1. 설정 관리, 분산 클러스터 관리, 명명 서비스, 분산 동기화, 분산 시스템에서 리더 선출, 중앙집중형 신뢰성 있는 데이터 저장소
        2. 주키퍼는 n 개의 서버로 단일 클러스터를 구성하며 이를 서버 앙상블이라고 함.
        3. 주키퍼 서비스는 복수의 서버에 복제되며, 모든 서버는 데이터 카피본을 저장
        4. Leader는 구동 시 주키퍼 내부 알고리즘에 의해 자동 선정
        5. Followers 서버들은 클라이언트로부터 받은 모든 업데이트 이벤트를 리더에게 전달함.
        6. 클라이언트는 모든 주키퍼 서버에서 읽을 수 있으며, 리더를 통해 쓸 수 있고 과반수 서버의 승인(합의)가 필요함.
4. **하둡 맵리듀스**
    1. Hadoop MapReduce는 구글 알고리즘 논문을 소프트웨어 프레임워크로 구현한 구현체
    2. Key-Value 구조가 알고리즘의 핵심
    3. 맵리듀스 알고리즘
        1. Map Function : (key1, value1) \> (key2, value2)
        2. Reduce Function : (key2, List of value2) \> (key3, value3)
        3. HDFS에 분산 저장되어 있는 데이터를 병렬로 처리하여 취합하는 역할
        4. Job에 대한 구동 및 관리는 하둡이 알아서 함 (개발자는 비즈니스 로직 구현에 집중)
    4. 맵리듀스의 장/단점
        1. 단순하고 사용이 편리
        2. 특정 데이터모델이나 스키마, 질의에 의존적이지 않은 유연성
        3. 저장 구조의 독립성
        4. 데이터 복제에 기반한 내구성과 재수행을 통한 내고장성 확보
        5. 높은 확장성
        6. 고정된 단일 데이터 흐름
        7. 기존 DBMS보다 불편한 스키마 질의
        8. 단순한 스케줄링
        9. 작은 데이터를 저장 / 처리하기에 적합하지 않음
        10. 개발도구의 불편함
        11. 기술지원의 어려움
    5. 맵리듀스의 구동 방식
        1. Local : 단일 JVM에서 전체 Job을 실행하는 방식
        2. Classic : Hadoop 버전 1.0 대까지 유지하던 맵리듀스 분산 처리 방식으로 Job Tracker와 Task Tracker를 사용하는 맵리듀스 버전1
        3. YARN : Hadoop 버전 2.0 이상에서 사용하는 맵리듀스 분산 처리 방식으로 맵리듀스 이외의 워크로드 수용이 가능한 맵리듀스 버전2
    6. 맵리듀스 1
        1. 주요 컴포넌트
            1. 클라이언트 : 구현된 맵리듀스 잡을 제출하는 실행 주체
            2. 잡트래커 : 맵리듀스 잡이 수행되는 전체 과정을 조정하며, 잡에 대한 마스터 역할 수행
            3. 태스크트래커 : 잡에 대한 분할된 태스크를 수행하며, 실질적인 데이터 프로세싱의 주체
            4. 하둡분산파일시스템 : 각 단계들 간의 데이터와 처리과정에서 발생하는 중간 파일들을 공유하기 위해 사용
        2. InputSplits : 물리적 Block 들을 논리적으로 그룹핑한 개념, Mapper의 입력으로 들어오는 데이터를 분할하는 방식을 제공하기 위해 데이터의 위치와 읽어들이는 길이를 정의함.
        3. 맵리듀스 구동절차
            <p align="center">
                <img src = "ImageForREADME/mapreduce.png", width="100%">
            </p>
        4. 맵리듀스 예제
            <p align="center">
                <img src = "ImageForREADME/mapreduceex.png", width="100%">
            </p>
5. **하둡 활용**


----------------------------------------------

## License
- I started the project after seeing [Tacademy's lecture](https://tacademy.skplanet.com/live/player/onlineLectureDetail.action?seq=188).