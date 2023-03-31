# flutter_cqlbr

```Delphi
void main() async {
  // Fake Cloud Firestore
  final instance1 = FakeFirebaseFirestore();

  await instance1.collection('users').doc('1').set(
    {
      'username': 'Bob 1',
      'sobrenome': 'Sobrenome Bob 1',
      'email': 'bob1@gmail.com',
    },
  );

  await instance1.collection('users').doc('2').set(
    {
      'username': 'Bob 2',
      'sobrenome': 'Sobrenome Bob 2',
      'email': 'bob2@gmail.com',
    },
  );

  // CQLBr
  CQLBr cqlbr = CQLBr(select: CQLSelectFirestore(instance1));
  
  test(
    'Test_Select_FirebaseFirestore',
    () async {
      final batch = instance1.batch();

      // CQLBr
      Query result1 = cqlbr.select$().all$().from$('users').asResult();
      QuerySnapshot snapshot1 = await result1.get();

      // Fake Cloud Firestore
      QuerySnapshot snapshot2 = await instance1.collection('users').get();

      await batch.commit();

      expect(snapshot1.docs.length, snapshot2.docs.length);
    },
  );

  test(
    'Test_Select_Where_FirebaseFirestore',
    () async {
      final batch = instance1.batch();

      // CQLBr
      Query result1 = cqlbr
          .select$()
          .all$()
          .from$('users')
          .where$('username')
          .equal$('Bob 1')
          .and$('email')
          .equal$('bob1@gmail.com')
          .or$('sobrenome')
          .equal$('Sobrenome Bob 1')
          .asResult();
      QuerySnapshot snapshot1 = await result1.get();

      // Fake Cloud Firestore
      Query result2 = instance1
          .collection('users')
          .where('username', isEqualTo: 'Bob 1')
          .where('email', isEqualTo: 'bob1@gmail.com')
          .where('sobrenome', isEqualTo: 'Sobrenome Bob 1');
      QuerySnapshot snapshot2 = await result2.get();

      await batch.commit();

      expect(snapshot1.docs.length, snapshot2.docs.length);
    },
  );
}
```
