# System.Net 의 IPAddress 자료형에 대해 이진탐색과 병합정렬

## 이진탐색
```C#

# 가정
# class ServerObject{ IPAddress Ip}
# class ParentServerObject { List<ServerObject> Servers}

public int IpBinarySearch(List<ServerObject> servers, IPAddress target)
        {
            if(servers == null)
                throw new ArgumentNullException("null");
            if (CompareTo(servers[0].Ip.GetAddressBytes(), target.GetAddressBytes()) == -1)
                return -1;
            if (CompareTo(servers[servers.Count-1].Ip.GetAddressBytes(), target.GetAddressBytes()) == 1)
                return -1;

            int upperBound = servers.Count;
            int lowerBound = 0;

            while(lowerBound<upperBound)
            {
                int mid = (upperBound + lowerBound) / 2;
                if(CompareTo(servers[mid].Ip.GetAddressBytes(), target.GetAddressBytes())==1)
                {
                    lowerBound = mid;
                }
                else if(CompareTo(servers[mid].Ip.GetAddressBytes(), target.GetAddressBytes()) == -1)
                {
                    upperBound = mid;
                }
                else
                {
                    return mid;
                }
            }
            return -1;


        }
```

```C#
// b2가 크면 1
        public int CompareTo(Byte[] b1, Byte[] b2)
        {
            for(int i =0; i< 4; i ++)
            {
                if(b1[i]<b2[i])
                {
                    return 1;                    
                }
                if (b1[i] > b2[i])
                {
                    return -1;
                }
            }
            return 0;
        }
```

## 병합정렬
```C#
public List<ServerObject> MergeSortUsingIP(List<ServerObject> unsortedServers)
        {
            if (unsortedServers.Count <= 1)
                return unsortedServers;
            List<ServerObject> left = new List<ServerObject>();
            List<ServerObject> right = new List<ServerObject>();

            int mid = unsortedServers.Count / 2;
            for(int i = 0; i< mid; i++)
            {
                left.Add(unsortedServers[i]);
            }
            for(int i = mid; i<unsortedServers.Count; i++)
            {
                right.Add(unsortedServers[i]);
            }

            left = MergeSortUsingIP(left);
            right = MergeSortUsingIP(right);


            return Merge(left, right);
        }
        
public List<ServerObject> Merge(List<ServerObject> left, List<ServerObject> right)
        {
            List<ServerObject> result = new List<ServerObject>();

            while(left.Count > 0 || right.Count > 0)
            {
                if(left.Count > 0 && right.Count > 0)
                {
                    if (CompareTo(left.First().Ip.GetAddressBytes(), right.First().Ip.GetAddressBytes()) ==1)
                    {
                        result.Add(left.First());
                        left.Remove(left.First());
                    }
                    else // 중복ip 없다는가정 -1리턴만한다는 가정
                    {
                        result.Add(right.First());
                        right.Remove(right.First());
                    }
                    
                }
                else if (left.Count > 0)
                {
                    result.Add(left.First());
                    left.Remove(left.First());
                }
                else if (right.Count > 0)
                {
                    result.Add(right.First());
                    right.Remove(right.First());
                }
            }
            return result;
        }      
```

- C#에서 List의 find 함수 사용 예 
```C#
# 찾고자하는 IPAddress ServerIp = new IPAddress~~~~~;
ServerObject targetServer = parentServerObject.Servers.Find(x => x.Ip.Equals(ServerIp);
```
- O(n) 임 

- 이진탐색 사용 예
```C#
ServerObject targetServer = parentServerObject.Servers[IpBinarySearch(parentServerObject.Servers, ServerIp)];
```
- O(logN) 임

- 이진탐색전에 병합정렬 사용 예 
```C#
parentServerObject.Servers = MergeSortUsingIP(parentServerObject.Servers);
```
