import java.io.*;
import java.math.BigInteger;
import java.nio.file.Files;
import java.nio.file.Paths;
import java.util.*;

public class SecretSharingSolver {
    
    static class Point {
        BigInteger x;
        BigInteger y;
        
        Point(BigInteger x, BigInteger y) {
            this.x = x;
            this.y = y;
        }
        
        @Override
        public String toString() {
            return "(" + x + ", " + y + ")";
        }
    }
    
    public static TestCase parseJsonFile(String filename) {
        try {
            String jsonContent = new String(Files.readAllBytes(Paths.get(filename)));
            return parseJsonString(jsonContent);
        } catch (IOException e) {
            System.err.println("Error reading file: " + e.getMessage());
            return null;
        }
    }
    
    public static TestCase parseJsonString(String jsonString) {
        try {
            jsonString = jsonString.replaceAll("\\s+", "");
            if (jsonString.startsWith("{")) {
                jsonString = jsonString.substring(1);
            }
            if (jsonString.endsWith("}")) {
                jsonString = jsonString.substring(0, jsonString.length() - 1);
            }
            
            String[] pairs = splitByMainCommas(jsonString);
            
            int n = 0, k = 0;
            List<Point> points = new ArrayList<>();
            
            for (String pair : pairs) {
                if (pair.trim().isEmpty()) continue;
                
                String[] keyValue = pair.split(":", 2);
                if (keyValue.length != 2) continue;
                
                String key = keyValue[0].replaceAll("\"", "").trim();
                String value = keyValue[1].trim();
                
                if ("keys".equals(key)) {
                    if (value.startsWith("{") && value.endsWith("}")) {
                        value = value.substring(1, value.length() - 1);
                    }
                    
                    String[] keyPairs = value.split(",");
                    for (String keyPair : keyPairs) {
                        String[] kv = keyPair.split(":");
                        if (kv.length == 2) {
                            String k1 = kv[0].replaceAll("\"", "").trim();
                            String v1 = kv[1].replaceAll("\"", "").trim();
                            
                            if ("n".equals(k1)) {
                                n = Integer.parseInt(v1);
                            } else if ("k".equals(k1)) {
                                k = Integer.parseInt(v1);
                            }
                        }
                    }
                } else {
                    try {
                        BigInteger x = new BigInteger(key);
                        
                        if (value.startsWith("{") && value.endsWith("}")) {
                            value = value.substring(1, value.length() - 1);
                        }
                        
                        int base = 10;
                        String pointValue = "";
                        
                        String[] attributes = value.split(",");
                        for (String attr : attributes) {
                            String[] attrPair = attr.split(":", 2);
                            if (attrPair.length == 2) {
                                String attrKey = attrPair[0].replaceAll("\"", "").trim();
                                String attrVal = attrPair[1].replaceAll("\"", "").trim();
                                
                                if ("base".equals(attrKey)) {
                                    base = Integer.parseInt(attrVal);
                                } else if ("value".equals(attrKey)) {
                                    pointValue = attrVal;
                                }
                            }
                        }
                        
                        BigInteger y = convertFromBase(pointValue, base);
                        points.add(new Point(x, y));
                        
                    } catch (NumberFormatException e) {
                    }
                }
            }
            
            return new TestCase(n, k, points);
            
        } catch (Exception e) {
            System.err.println("Error parsing JSON: " + e.getMessage());
            e.printStackTrace();
            return null;
        }
    }
    
    private static String[] splitByMainCommas(String str) {
        List<String> parts = new ArrayList<>();
        StringBuilder current = new StringBuilder();
        int braceDepth = 0;
        
        for (int i = 0; i < str.length(); i++) {
            char c = str.charAt(i);
            
            if (c == '{') {
                braceDepth++;
            } else if (c == '}') {
                braceDepth--;
            } else if (c == ',' && braceDepth == 0) {
                parts.add(current.toString());
                current = new StringBuilder();
                continue;
            }
            
            current.append(c);
        }
        
        if (current.length() > 0) {
            parts.add(current.toString());
        }
        
        return parts.toArray(new String[0]);
    }
    
    public static BigInteger convertFromBase(String value, int base) {
        if (base == 10) {
            return new BigInteger(value);
        }
        return new BigInteger(value, base);
    }
    
    public static BigInteger findSecret(List<Point> allPoints, int k) {
        List<Point> points = allPoints.subList(0, Math.min(k, allPoints.size()));
        BigInteger secret = BigInteger.ZERO;
        
        for (int i = 0; i < points.size(); i++) {
            Point pi = points.get(i);
            BigInteger numerator = BigInteger.ONE;
            BigInteger denominator = BigInteger.ONE;
            
            for (int j = 0; j < points.size(); j++) {
                if (i != j) {
                    Point pj = points.get(j);
                    numerator = numerator.multiply(BigInteger.ZERO.subtract(pj.x));
                    denominator = denominator.multiply(pi.x.subtract(pj.x));
                }
            }
            
            BigInteger contribution = pi.y.multiply(numerator).divide(denominator);
            secret = secret.add(contribution);
        }
        
        return secret;
    }
    
    static class TestCase {
        int n, k;
        List<Point> points;
        
        TestCase(int n, int k, List<Point> points) {
            this.n = n;
            this.k = k;
            this.points = points;
        }
        
        @Override
        public String toString() {
            return String.format("TestCase{n=%d, k=%d, points=%s}", n, k, points);
        }
    }
    
    public static void createTestFiles() {
        String testCase1 = """
        {
            "keys": {
                "n": 4,
                "k": 3
            },
            "1": {
                "base": "10",
                "value": "4"
            },
            "2": {
                "base": "2",
                "value": "111"
            },
            "3": {
                "base": "10",
                "value": "12"
            },
            "6": {
                "base": "4",
                "value": "213"
            }
        }
        """;
        
        String testCase2 = """
        {
            "keys": {
                "n": 10,
                "k": 7
            },
            "1": {
                "base": "6",
                "value": "13444211440455345511"
            },
            "2": {
                "base": "15",
                "value": "aed7015a346d63"
            },
            "3": {
                "base": "15",
                "value": "6aeeb69631c227c"
            },
            "4": {
                "base": "16",
                "value": "e1b5e05623d881f"
            },
            "5": {
                "base": "8",
                "value": "316034514573652620673"
            },
            "6": {
                "base": "3",
                "value": "2122212201122002221120200210011020220200"
            },
            "7": {
                "base": "3",
                "value": "20120221122211000100210021102001201112121"
            },
            "8": {
                "base": "6",
                "value": "20220554335330240002224253"
            },
            "9": {
                "base": "12",
                "value": "45153788322a1255483"
            },
            "10": {
                "base": "7",
                "value": "1101613130313526312514143"
            }
        }
        """;
        
        try {
            Files.write(Paths.get("testcase1.json"), testCase1.getBytes());
            Files.write(Paths.get("testcase2.json"), testCase2.getBytes());
            System.out.println("Test files created: testcase1.json and testcase2.json");
        } catch (IOException e) {
            System.err.println("Error creating test files: " + e.getMessage());
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== Shamir's Secret Sharing Solver ===\n");
        createTestFiles();
        System.out.println("=== Test Case 1 ===");
        TestCase tc1 = parseJsonFile("testcase1.json");
        if (tc1 != null) {
            System.out.println("Parsed data: n=" + tc1.n + ", k=" + tc1.k);
            System.out.println("Points after decoding:");
            for (Point p : tc1.points) {
                System.out.println("  " + p);
            }
            BigInteger secret1 = findSecret(tc1.points, tc1.k);
            System.out.println("Secret (c): " + secret1);
        }
        System.out.println("\n" + "=".repeat(50));
        System.out.println("\n=== Test Case 2 ===");
        TestCase tc2 = parseJsonFile("testcase2.json");
        if (tc2 != null) {
            System.out.println("Parsed data: n=" + tc2.n + ", k=" + tc2.k);
            System.out.println("Points after decoding:");
            for (Point p : tc2.points) {
                System.out.println("  " + p);
            }
            BigInteger secret2 = findSecret(tc2.points, tc2.k);
            System.out.println("Secret (c): " + secret2);
        }
        System.out.println("\n=== Instructions ===");
        System.out.println("1. The program reads JSON files containing encoded polynomial roots");
        System.out.println("2. It decodes y-values from different bases to decimal");
        System.out.println("3. Uses Lagrange interpolation to find the polynomial's constant term");
        System.out.println("4. The secret is the value of the polynomial at x=0 (constant term)");
        System.out.println("5. Only the first 'k' points are needed to solve the polynomial");
    }
}
