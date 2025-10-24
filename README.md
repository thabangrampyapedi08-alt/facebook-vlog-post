"use client";

import { useState, useEffect, useMemo, useCallback } from "react";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { Card, CardContent } from "@/components/ui/card";
import { Search, Copy } from "lucide-react";

// Rich lifestyle and flirtatious content templates
const richLifestyleTemplates = [
  "Want to live like the rich and famous? Join our exclusive community and discover the secrets to a luxurious lifestyle!",
  "Why settle for ordinary when you can have extraordinary? Our members enjoy private jets, yachts, and 5-star resorts.",
  "Don't let mediocrity define your life. Break free from the 9-5 grind and join the elite circle of success.",
  "Imagine waking up in a penthouse suite with champagne and caviar for breakfast. This could be your reality.",
  "Stop dreaming about wealth and start living it! Our proven system will transform your life in 30 days.",
  "You're too special to live an average life. Come experience the luxury you deserve.",
  "Why watch others live their best life when you can be living yours? Join us today!",
  "Escape the rat race and join the ranks of the wealthy elite. Your future self will thank you.",
  "Living rich isn't just about money - it's about mindset. Learn how to think like a millionaire.",
  "Don't just survive - thrive! Our community teaches you how to attract abundance effortlessly."
];

const flirtatiousTemplates = [
  "Feeling lonely in your ordinary life? Come experience the excitement you've been missing!",
  "Why date average people when you can attract the most desirable partners? We'll show you how.",
  "Stop swiping left and start living right! Our members attract the most attractive people effortlessly.",
  "Ready to upgrade your love life? Join our exclusive dating circle where the wealthy and beautiful meet.",
  "Don't settle for boring relationships when you can have passionate, exciting connections.",
  "Imagine having admirers from all walks of life. Our techniques will make you irresistible.",
  "Tired of being ignored? Learn the secrets that make people chase you instead.",
  "Confidence is sexy, and we'll teach you how to radiate it. Attract the attention you deserve.",
  "Why be ordinary when you can be extraordinary? Become the person everyone wants to be with.",
  "Stop waiting for love to find you - go out and attract it! Our methods work guaranteed."
];

// Mock data generation for posts
const generateMockPosts = (count: number) => {
  const posts = [];
  const topics = [
    "Wealth Building", "Luxury Lifestyle", "Success Mindset", 
    "Dating Secrets", "Confidence Boost", "Attracting Abundance"
  ];
  
  const adjectives = [
    "Proven", "Secret", "Exclusive", "Luxury", "Elite", 
    "Powerful", "Irresistible", "Life-Changing"
  ];
  
  const actions = [
    "Join now!", "Don't miss out!", "Limited spots!",
    "Act fast!", "Your future awaits!", "Transform today!",
    "Unlock secrets!", "Discover more!"
  ];

  for (let i = 1; i <= count; i++) {
    const topic = topics[Math.floor(Math.random() * topics.length)];
    const adjective = adjectives[Math.floor(Math.random() * adjectives.length)];
    const action = actions[Math.floor(Math.random() * actions.length)];
    
    // Alternate between rich lifestyle and flirtatious content
    const isFlirtContent = i % 3 === 0;
    const templates = isFlirtContent ? flirtatiousTemplates : richLifestyleTemplates;
    const contentTemplate = templates[Math.floor(Math.random() * templates.length)];
    
    posts.push({
      id: i,
      title: `${adjective} ${topic} Secrets`,
      content: `${contentTemplate} ${action} #${topic.replace(/\s+/g, '')} #Lifestyle #Success`,
      category: isFlirtContent ? "flirt" : "rich"
    });
  }
  
  return posts;
};

// Virtualized list component for performance
const VirtualizedList = ({ 
  items, 
  itemHeight, 
  windowHeight,
  renderItem 
}: {
  items: any[];
  itemHeight: number;
  windowHeight: number;
  renderItem: (item: any, index: number) => React.ReactNode;
}) => {
  const [scrollTop, setScrollTop] = useState(0);
  
  const visibleItems = useMemo(() => {
    const startIndex = Math.floor(scrollTop / itemHeight);
    const visibleCount = Math.ceil(windowHeight / itemHeight) + 5; // Buffer
    return items.slice(startIndex, startIndex + visibleCount);
  }, [scrollTop, items, itemHeight, windowHeight]);

  const totalHeight = items.length * itemHeight;
  const offsetY = Math.floor(scrollTop / itemHeight) * itemHeight;

  return (
    <div 
      className="w-full overflow-y-auto"
      style={{ height: windowHeight }}
      onScroll={(e) => setScrollTop(e.currentTarget.scrollTop)}
    >
      <div style={{ height: totalHeight, position: 'relative' }}>
        <div style={{ 
          transform: `translateY(${offsetY}px)`,
          position: 'absolute',
          top: 0,
          left: 0,
          right: 0
        }}>
          {visibleItems.map((item, idx) => renderItem(item, idx))}
        </div>
      </div>
    </div>
  );
};

export default function VlogApp() {
  const [searchQuery, setSearchQuery] = useState("");
  const [copiedId, setCopiedId] = useState<number | null>(null);
  const [posts, setPosts] = useState<any[]>([]);
  const [loading, setLoading] = useState(true);
  const [activeCategory, setActiveCategory] = useState<"all" | "rich" | "flirt">("all");

  // Generate mock posts on mount
  useEffect(() => {
    // Simulate loading delay
    const timer = setTimeout(() => {
      const mockPosts = generateMockPosts(1000); // Using 1000 for demo
      setPosts(mockPosts);
      setLoading(false);
    }, 800);
    
    return () => clearTimeout(timer);
  }, []);

  // Filter posts based on search query and category
  const filteredPosts = useMemo(() => {
    let result = posts;
    
    // Apply category filter
    if (activeCategory !== "all") {
      result = result.filter(post => post.category === activeCategory);
    }
    
    // Apply search filter
    if (searchQuery) {
      const query = searchQuery.toLowerCase();
      result = result.filter(post => 
        post.title.toLowerCase().includes(query) || 
        post.content.toLowerCase().includes(query)
      );
    }
    
    return result;
  }, [posts, searchQuery, activeCategory]);

  // Handle copy to clipboard
  const handleCopy = useCallback(async (text: string, id: number) => {
    try {
      await navigator.clipboard.writeText(text);
      setCopiedId(id);
      setTimeout(() => setCopiedId(null), 2000);
    } catch (err) {
      console.error('Failed to copy text: ', err);
    }
  }, []);

  // Generate 80 million posts (simulated)
  const handleGeneratePosts = useCallback(() => {
    setLoading(true);
    // Simulate generating large dataset
    setTimeout(() => {
      const mockPosts = generateMockPosts(5000); // Simulated large dataset
      setPosts(mockPosts);
      setLoading(false);
    }, 1000);
  }, []);

  // Render individual post item
  const renderPost = (post: any) => (
    <Card key={post.id} className="mb-4 border border-gray-200 shadow-sm">
      <CardContent className="p-4">
        <div className="flex justify-between items-start">
          <div className="flex-1 min-w-0">
            <div className="flex items-center mb-2">
              <span className={`inline-block w-3 h-3 rounded-full mr-2 ${
                post.category === "rich" ? "bg-green-500" : "bg-pink-500"
              }`}></span>
              <h3 className="font-semibold text-lg truncate">{post.title}</h3>
            </div>
            <p className="text-gray-700 whitespace-pre-wrap break-words">
              {post.content}
            </p>
          </div>
          <Button
            variant="outline"
            size="sm"
            className="ml-4 flex-shrink-0"
            onClick={() => handleCopy(post.content, post.id)}
          >
            <Copy className={`h-4 w-4 ${copiedId === post.id ? 'text-green-500' : ''}`} />
            <span className="sr-only">Copy</span>
          </Button>
        </div>
        {copiedId === post.id && (
          <div className="mt-2 text-sm text-green-600 flex items-center">
            <span>Copied to clipboard!</span>
          </div>
        )}
      </CardContent>
    </Card>
  );

  return (
    <div className="min-h-screen bg-gray-50 py-8">
      <div className="max-w-4xl mx-auto px-4">
        <header className="mb-8 text-center">
          <h1 className="text-3xl font-bold text-gray-900">Vlog Text Posts</h1>
          <p className="mt-2 text-gray-600">
            Browse and copy text posts for your Facebook monetization
          </p>
        </header>

        <div className="mb-6 flex flex-col sm:flex-row gap-4">
          <div className="relative flex-1">
            <Search className="absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-400 h-4 w-4" />
            <Input
              type="text"
              placeholder="Search posts..."
              className="pl-10 py-6 text-lg"
              value={searchQuery}
              onChange={(e) => setSearchQuery(e.target.value)}
            />
          </div>
          
          <Button 
            onClick={handleGeneratePosts}
            className="bg-gradient-to-r from-purple-600 to-indigo-700 hover:from-purple-700 hover:to-indigo-800 text-white py-6 px-6 text-lg font-bold whitespace-nowrap"
          >
            AUTOMOTIC THEM
          </Button>
        </div>

        <div className="mb-6 flex flex-wrap gap-2">
          <Button 
            variant={activeCategory === "all" ? "default" : "outline"}
            onClick={() => setActiveCategory("all")}
            className="px-4"
          >
            All Posts
          </Button>
          <Button 
            variant={activeCategory === "rich" ? "default" : "outline"}
            onClick={() => setActiveCategory("rich")}
            className="px-4"
          >
            Rich Lifestyle
          </Button>
          <Button 
            variant={activeCategory === "flirt" ? "default" : "outline"}
            onClick={() => setActiveCategory("flirt")}
            className="px-4"
          >
            Flirt & Dating
          </Button>
        </div>

        {loading ? (
          <div className="flex justify-center items-center h-64">
            <div className="animate-spin rounded-full h-12 w-12 border-t-2 border-b-2 border-blue-500"></div>
          </div>
        ) : (
          <>
            <div className="mb-4 text-sm text-gray-500">
              Showing {filteredPosts.length} posts
            </div>
            
            <VirtualizedList
              items={filteredPosts}
              itemHeight={200}
              windowHeight={600}
              renderItem={renderPost}
            />
          </>
        )}
      </div>
    </div>
  );
}
